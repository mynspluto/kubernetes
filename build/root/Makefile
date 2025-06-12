# Kubernetes Makefile 주석 설명

# Copyright 2016 The Kubernetes Authors.
# Kubernetes 프로젝트의 저작권 표시

# Licensed under the Apache License, Version 2.0 (the "License");
# Apache 2.0 라이선스 하에 배포됨을 명시

# 라이선스 관련 표준 헤더 (생략)

DBG_MAKEFILE ?=
# Makefile 디버깅 플래그 변수 정의 (기본값 없음)

ifeq ($(DBG_MAKEFILE),1)
# DBG_MAKEFILE이 1이면 디버깅 모드 활성화
    $(warning ***** starting Makefile for goal(s) "$(MAKECMDGOALS)")
    # 실행할 타겟 목표들을 경고 메시지로 출력
    $(warning ***** $(shell date))
    # 현재 시간을 경고 메시지로 출력
else
    # 디버깅 모드가 아니면
    MAKEFLAGS += -s
    # Make 실행 시 명령어 출력 안함 (silent 모드)
endif

# Old-skool build tools.
# 전통적인 빌드 도구들

# Commonly used targets (see each target for more information):
# 자주 사용되는 타겟들 설명

#   all: Build code.
# all 타겟: 코드 빌드

#   test: Run tests.
# test 타겟: 테스트 실행

#   clean: Clean up.
# clean 타겟: 정리 작업

# It's necessary to set this because some environments don't link sh -> bash.
# 일부 환경에서 sh가 bash로 링크되지 않아서 명시적 설정 필요
SHELL := /usr/bin/env bash -o errexit -o pipefail -o nounset
# 쉘을 bash로 설정, 에러 시 중단, 파이프 실패 시 중단, 미정의 변수 사용 시 에러

BASH_ENV := ./hack/lib/logging.sh
# Bash 환경 설정 파일 지정 (로깅 관련)

# Define optional user-input variables so `make --warn-undefined-variables` works.
# 사용자 입력 변수들을 선택적으로 정의 (미정의 변수 경고 방지)

# Usage is described below with each target that respects these
# 사용법은 아래 각 타겟에서 설명됨

PRINT_HELP ?=
# 도움말 출력 여부 변수

WHAT ?=
# 빌드할 대상 지정 변수

TESTS ?=
# 실행할 테스트 지정 변수

BRANCH ?=
# Git 브랜치 지정 변수

# We don't need make's built-in rules.
# Make의 내장 규칙들 사용 안함
MAKEFLAGS += --no-builtin-rules

# Be pedantic about undefined variables.
# 미정의 변수에 대해 엄격하게 처리
MAKEFLAGS += --warn-undefined-variables

.SUFFIXES:
# 접미사 규칙 비활성화 (성능 향상)

# Constants used throughout.
# 전체적으로 사용되는 상수들

.EXPORT_ALL_VARIABLES:
# 모든 변수를 하위 프로세스로 내보내기

OUT_DIR ?= _output
# 출력 디렉토리 설정 (기본값: _output)

BIN_DIR := $(OUT_DIR)/bin
# 바이너리 파일 출력 디렉토리

ifdef KUBE_GOFLAGS
# KUBE_GOFLAGS가 정의되어 있으면
$(info KUBE_GOFLAGS is now deprecated. Please use GOFLAGS instead.)
# 사용 중단 메시지 출력
ifndef GOFLAGS
# GOFLAGS가 정의되지 않았으면
GOFLAGS := $(KUBE_GOFLAGS)
# KUBE_GOFLAGS 값을 GOFLAGS로 복사
unexport KUBE_GOFLAGS
# KUBE_GOFLAGS 환경변수 제거
else
# GOFLAGS가 이미 정의되어 있으면
$(error Both KUBE_GOFLAGS and GOFLAGS are set. Please use just GOFLAGS)
# 에러 메시지 출력하고 중단
endif
endif

# This controls the verbosity of the build.  Higher numbers mean more output.
# 빌드 출력 상세도 제어. 숫자가 클수록 더 많은 출력
KUBE_VERBOSE ?= 1

define ALL_HELP_INFO
# ALL 타겟 도움말 정보 정의 시작
# Build code.
# 코드 빌드

# Args:
# 인수들:

#   WHAT: Directory or Go package names to build.
# WHAT: 빌드할 디렉토리 또는 Go 패키지 이름

#   If any of these directories has a 'main' package, the build will produce executable files under $(OUT_DIR)/bin.
# 이 디렉토리들에 'main' 패키지가 있으면 $(OUT_DIR)/bin 하위에 실행파일 생성

#   If not specified, "everything" will be built.
# 지정하지 않으면 "모든 것"이 빌드됨

#     "vendor/<module>/<path>" is accepted as alias for "<module>/<path>".
# "vendor/<module>/<path>" 형식은 "<module>/<path>"의 별칭으로 허용

#     "ginkgo" is an alias for the ginkgo CLI.
# "ginkgo"는 ginkgo CLI의 별칭

#   GOFLAGS: Extra flags to pass to 'go' when building.
# GOFLAGS: 빌드 시 'go' 명령에 전달할 추가 플래그

#   GOLDFLAGS: Extra linking flags passed to 'go' when building.
# GOLDFLAGS: 빌드 시 'go' 명령에 전달할 추가 링킹 플래그

#   GOGCFLAGS: Additional go compile flags passed to 'go' when building.
# GOGCFLAGS: 빌드 시 'go' 명령에 전달할 추가 컴파일 플래그

#   DBG: If set to "1", build with optimizations disabled for easier debugging.
# DBG: "1"로 설정하면 디버깅을 위해 최적화 비활성화

#   Any other value is ignored.
# 다른 값은 무시됨

# Example:
# 예제:

#   make
# make

#   make all
# make all

#   make all WHAT=cmd/kubelet GOFLAGS=-v
# make all WHAT=cmd/kubelet GOFLAGS=-v

#   make all DBG=1
# make all DBG=1

#     Note: Specify DBG=1 for building unstripped binaries
# 참고: DBG=1을 지정하면 스트립되지 않은 바이너리 생성

#     which allows you to use code debugging tools like delve.
# 이렇게 하면 delve 같은 코드 디버깅 도구 사용 가능

#     When DBG is unspecified, it defaults to "-s -w" which strips debug information.
# DBG가 지정되지 않으면 기본값 "-s -w"로 디버그 정보 제거
endef
# 도움말 정보 정의 끝

.PHONY: all
# all 타겟을 phony 타겟으로 선언 (실제 파일이 아님)

ifeq ($(PRINT_HELP),y)
# PRINT_HELP가 y이면
all:
	echo "$$ALL_HELP_INFO"
	# 도움말 정보 출력
else
# 그렇지 않으면
all:
	hack/make-rules/build.sh $(WHAT)
	# 실제 빌드 스크립트 실행
endif

define GINKGO_HELP_INFO
# GINKGO 타겟 도움말 정보
# Build ginkgo
# ginkgo 빌드

# Example:
# 예제:
# make ginkgo
# make ginkgo
endef

.PHONY: ginkgo
# ginkgo 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
ginkgo:
	echo "$$GINKGO_HELP_INFO"
	# ginkgo 도움말 출력
else
# 그렇지 않으면
ginkgo:
	hack/make-rules/build.sh github.com/onsi/ginkgo/v2/ginkgo
	# ginkgo v2 CLI 빌드
endif

define VERIFY_HELP_INFO
# VERIFY 타겟 도움말 정보
# Runs all the presubmission verifications.
# 모든 사전 제출 검증 실행

# Args:
# 인수들:

#   BRANCH: Branch to be passed to verify-vendor.sh script.
# BRANCH: verify-vendor.sh 스크립트에 전달할 브랜치

#   WHAT: List of checks to run
# WHAT: 실행할 검사 목록

# Example:
# 예제:

#   make verify
# make verify

#   make verify BRANCH=branch_x
# make verify BRANCH=branch_x

#   make verify WHAT="gofmt typecheck"
# make verify WHAT="gofmt typecheck"
endef

.PHONY: verify
# verify 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
verify:
	echo "$$VERIFY_HELP_INFO"
	# verify 도움말 출력
else ifeq ($(origin KUBE_VERIFY_GIT_BRANCH), undefined)
# KUBE_VERIFY_GIT_BRANCH가 정의되지 않았으면
verify:
	KUBE_VERIFY_GIT_BRANCH=$(BRANCH) hack/make-rules/verify.sh
	# BRANCH 변수를 환경변수로 설정하고 검증 스크립트 실행
else
# KUBE_VERIFY_GIT_BRANCH가 이미 정의되어 있으면
verify:
	hack/make-rules/verify.sh
	# 그냥 검증 스크립트 실행
endif

define QUICK_VERIFY_HELP_INFO
# QUICK_VERIFY 타겟 도움말 정보
# Runs only the presubmission verifications that aren't slow.
# 느리지 않은 사전 제출 검증만 실행

# Example:
# 예제:
#   make quick-verify
# make quick-verify
endef

.PHONY: quick-verify
# quick-verify 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
quick-verify:
	echo "$$QUICK_VERIFY_HELP_INFO"
	# quick-verify 도움말 출력
else
# 그렇지 않으면
quick-verify:
	QUICK=true SILENT=false hack/make-rules/verify.sh
	# 빠른 모드로 검증 스크립트 실행
endif

define UPDATE_HELP_INFO
# UPDATE 타겟 도움말 정보
# Runs all the update scripts.
# 모든 업데이트 스크립트 실행

# Example:
# 예제:
# make update
# make update
endef

.PHONY: update
# update 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
update:
	echo "$$UPDATE_HELP_INFO"
	# update 도움말 출력
else
# 그렇지 않으면
update:
	hack/make-rules/update.sh
	# 업데이트 스크립트 실행
endif

define CHECK_TEST_HELP_INFO
# CHECK_TEST 타겟 도움말 정보
# Build and run tests.
# 빌드하고 테스트 실행

# Args:
# 인수들:

#   WHAT: Directory names to test.
# WHAT: 테스트할 디렉토리 이름들

#   All *_test.go files under these directories will be run.
# 이 디렉토리들 하위의 모든 *_test.go 파일들이 실행됨

#   If not specified, "everything" will be tested.
# 지정하지 않으면 "모든 것"이 테스트됨

#   TESTS: Same as WHAT.
# TESTS: WHAT과 동일

#   KUBE_COVER: Whether to run tests with code coverage.
# KUBE_COVER: 코드 커버리지와 함께 테스트 실행 여부

#   Set to 'y' to enable coverage collection.
# 'y'로 설정하면 커버리지 수집 활성화

#   GOFLAGS: Extra flags to pass to 'go' when building.
# GOFLAGS: 빌드 시 'go'에 전달할 추가 플래그

#   GOLDFLAGS: Extra linking flags to pass to 'go' when building.
# GOLDFLAGS: 빌드 시 'go'에 전달할 추가 링킹 플래그

#   GOGCFLAGS: Additional go compile flags passed to 'go' when building.
# GOGCFLAGS: 빌드 시 'go'에 전달할 추가 컴파일 플래그

# Example:
# 예제:

#   make check
# make check

#   make test
# make test

#   make check WHAT=./pkg/kubelet GOFLAGS=-v
# make check WHAT=./pkg/kubelet GOFLAGS=-v
endef

.PHONY: check test
# check와 test 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
check test:
	echo "$$CHECK_TEST_HELP_INFO"
	# check/test 도움말 출력
else
# 그렇지 않으면
check test:
	hack/make-rules/test.sh $(WHAT) $(TESTS)
	# 테스트 스크립트 실행 (WHAT과 TESTS 변수 전달)
endif

define TEST_IT_HELP_INFO
# TEST_IT 타겟 도움말 정보
# Build and run integration tests.
# 통합 테스트 빌드하고 실행

# Args:
# 인수들:

#   WHAT: Directory names to test.
# WHAT: 테스트할 디렉토리 이름들

#   All *_test.go files under these directories will be run.
# 이 디렉토리들 하위의 모든 *_test.go 파일들이 실행됨

#   If not specified, "everything" will be tested.
# 지정하지 않으면 "모든 것"이 테스트됨

#   KUBE_TEST_ARGS: Arguments to pass to the resulting go test invocation.
# KUBE_TEST_ARGS: 결과 go test 호출에 전달할 인수들

# Example:
# 예제:

#   make test-integration
# make test-integration

#   make test-integration WHAT=./test/integration/kubelet GOFLAGS="-v -coverpkg=./pkg/kubelet/..." KUBE_COVER="y"
# make test-integration WHAT=./test/integration/kubelet GOFLAGS="-v -coverpkg=./pkg/kubelet/..." KUBE_COVER="y"

#   make test-integration WHAT=./test/integration/pods GOFLAGS="-v" KUBE_TEST_ARGS='-run ^TestPodUpdateActiveDeadlineSeconds$$'
# make test-integration WHAT=./test/integration/pods GOFLAGS="-v" KUBE_TEST_ARGS='-run ^TestPodUpdateActiveDeadlineSeconds$$'
endef

.PHONY: test-integration
# test-integration 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
test-integration:
	echo "$$TEST_IT_HELP_INFO"
	# test-integration 도움말 출력
else
# 그렇지 않으면
test-integration:
	# KUBE_TEST_ARGS is explicitly passed here in order to ensure that we are preserving any dollar signs in the value.
	# KUBE_TEST_ARGS를 명시적으로 전달하여 값의 달러 기호를 보존
	KUBE_TEST_ARGS='$(value KUBE_TEST_ARGS)' hack/make-rules/test-integration.sh $(WHAT)
	# 통합 테스트 스크립트 실행
endif

define TEST_E2E_NODE_HELP_INFO
# TEST_E2E_NODE 타겟 도움말 정보 (매우 긴 도움말이므로 주요 부분만 주석)
# Build and run node end-to-end tests.
# 노드 end-to-end 테스트 빌드하고 실행

# Args:
# 인수들:

#  FOCUS: Regexp that matches the tests to be run. Defaults to "".
# FOCUS: 실행할 테스트와 매치되는 정규식. 기본값 ""

#  SKIP: Regexp that matches the tests that needs to be skipped.
# SKIP: 건너뛸 테스트와 매치되는 정규식

#    Defaults to "\[Flaky\]|\[Slow\]|\[Serial\]" unless LABEL_FILTER is set.
# LABEL_FILTER가 설정되지 않으면 기본값 "\[Flaky\]|\[Slow\]|\[Serial\]"

# ... (여러 인수들과 설명들 - 원격 실행, 타임아웃, 병렬성 등)

# Example:
# 예제:

#   make test-e2e-node FOCUS=Kubelet SKIP=container
# make test-e2e-node FOCUS=Kubelet SKIP=container

#   make test-e2e-node REMOTE=true DELETE_INSTANCES=true
# make test-e2e-node REMOTE=true DELETE_INSTANCES=true

#   make test-e2e-node TEST_ARGS='--kubelet-flags="--cgroups-per-qos=true"'
# make test-e2e-node TEST_ARGS='--kubelet-flags="--cgroups-per-qos=true"'

# Build and run tests.
# 테스트 빌드하고 실행
endef

.PHONY: test-e2e-node
# test-e2e-node 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
test-e2e-node:
	echo "$$TEST_E2E_NODE_HELP_INFO"
	# test-e2e-node 도움말 출력
else
# 그렇지 않으면
test-e2e-node: ginkgo
	# ginkgo가 먼저 빌드되어야 함 (의존성)
	hack/make-rules/test-e2e-node.sh
	# e2e 노드 테스트 스크립트 실행
endif

define TEST_CMD_HELP_INFO
# TEST_CMD 타겟 도움말 정보
# Build and run cmdline tests.
# 명령행 테스트 빌드하고 실행

# Args:
# 인수들:

#   WHAT: List of tests to run, check test/cmd/legacy-script.sh for names.
# WHAT: 실행할 테스트 목록, 이름은 test/cmd/legacy-script.sh 확인

#     For example, WHAT=deployment will run run_deployment_tests function.
# 예를 들어, WHAT=deployment는 run_deployment_tests 함수 실행

# Example:
# 예제:

#   make test-cmd
# make test-cmd

#   make test-cmd WHAT="deployment impersonation"
# make test-cmd WHAT="deployment impersonation"
endef

.PHONY: test-cmd
# test-cmd 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
test-cmd:
	echo "$$TEST_CMD_HELP_INFO"
	# test-cmd 도움말 출력
else
# 그렇지 않으면
test-cmd:
	hack/make-rules/test-cmd.sh
	# 명령행 테스트 스크립트 실행
endif

define CLEAN_HELP_INFO
# CLEAN 타겟 도움말 정보
# Remove all build artifacts.
# 모든 빌드 아티팩트 제거

# Example:
# 예제:
#   make clean
# make clean
endef

.PHONY: clean
# clean 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
clean:
	echo "$$CLEAN_HELP_INFO"
	# clean 도움말 출력
else
# 그렇지 않으면
clean:
	build/make-clean.sh
	# 빌드 정리 스크립트 실행
	hack/make-rules/clean.sh
	# 일반 정리 스크립트 실행
endif

define VET_HELP_INFO
# VET 타겟 도움말 정보
# Run 'go vet'.
# 'go vet' 실행

# Args:
# 인수들:

#   WHAT: Directory names to vet.
# WHAT: vet을 실행할 디렉토리 이름들

#   All *.go files under these directories will be vetted.
# 이 디렉토리들 하위의 모든 *.go 파일들이 검사됨

#   If not specified, "everything" will be vetted.
# 지정하지 않으면 "모든 것"이 검사됨

# Example:
# 예제:

#   make vet
# make vet

#   make vet WHAT=./pkg/kubelet
# make vet WHAT=./pkg/kubelet
endef

.PHONY: vet
# vet 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
vet:
	echo "$$VET_HELP_INFO"
	# vet 도움말 출력
else
# 그렇지 않으면
vet:
	hack/make-rules/vet.sh $(WHAT)
	# vet 스크립트 실행 (WHAT 변수 전달)
endif

define LINT_HELP_INFO
# LINT 타겟 도움말 정보
# Run golangci-lint
# golangci-lint 실행

# Example:
# 예제:
#   make lint
# make lint
endef

.PHONY: lint
# lint 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
lint:
	echo "$$LINT_HELP_INFO"
	# lint 도움말 출력
else
# 그렇지 않으면
lint:
	hack/verify-golangci-lint.sh
	# golangci-lint 검증 스크립트 실행
endif

define RELEASE_HELP_INFO
# RELEASE 타겟 도움말 정보
# Build a release
# 릴리스 빌드

# Use the 'release-in-a-container' target to build the release when already in
# 이미 컨테이너 안에 있을 때는 'release-in-a-container' 타겟 사용

# a container vs. creating a new container to build in using the 'release'
# 'release' 타겟을 사용해서 새 컨테이너를 만들어 빌드하는 것과 대조

# target.  Useful for running in GCB.
# GCB에서 실행할 때 유용함

# Example:
# 예제:

#   make release
# make release

#   make release-in-a-container
# make release-in-a-container
endef

.PHONY: release release-in-a-container
# release와 release-in-a-container 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
release release-in-a-container:
	echo "$$RELEASE_HELP_INFO"
	# release 도움말 출력
else
# 그렇지 않으면
release release-in-a-container: KUBE_BUILD_CONFORMANCE = y
# 두 타겟 모두 KUBE_BUILD_CONFORMANCE를 y로 설정

release:
	build/release.sh
	# 릴리스 스크립트 실행

release-in-a-container:
	build/release-in-a-container.sh
	# 컨테이너 내 릴리스 스크립트 실행
endif

define RELEASE_IMAGES_HELP_INFO
# RELEASE_IMAGES 타겟 도움말 정보
# Build release images
# 릴리스 이미지 빌드

# Args:
# 인수들:

#   KUBE_BUILD_CONFORMANCE: Whether to build conformance testing image as well.
# KUBE_BUILD_CONFORMANCE: 컨포먼스 테스트 이미지도 빌드할지 여부

#   Set to 'n' to skip.
# 'n'으로 설정하면 건너뜀

#   DBG: If set to "1", build with optimizations disabled for easier debugging.
# DBG: "1"로 설정하면 디버깅을 위해 최적화 비활성화

#   Any other value is ignored.
# 다른 값은 무시됨

# Example:
# 예제:

#   make release-images
# make release-images

#   make release-images DBG=1
# make release-images DBG=1

#     Note: Specify DBG=1 for building unstripped binaries
# 참고: DBG=1을 지정하면 스트립되지 않은 바이너리 빌드

#           which allows you to use code debugging tools like delve.
# 이렇게 하면 delve 같은 코드 디버깅 도구 사용 가능

#           When DBG is unspecified, it defaults to "-s -w" which strips debug information.
# DBG가 지정되지 않으면 기본값 "-s -w"로 디버그 정보 제거
endef

.PHONY: release-images
# release-images 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
release-images: KUBE_BUILD_CONFORMANCE = y
# KUBE_BUILD_CONFORMANCE를 y로 설정
release-images:
	echo "$$RELEASE_IMAGES_HELP_INFO"
	# release-images 도움말 출력
else
# 그렇지 않으면
release-images:
	build/release-images.sh
	# 릴리스 이미지 빌드 스크립트 실행
endif

define RELEASE_SKIP_TESTS_HELP_INFO
# RELEASE_SKIP_TESTS 타겟 도움말 정보
# Build a release, but skip tests
# 릴리스 빌드하되 테스트는 건너뜀

# Args:
# 인수들:

#   KUBE_RELEASE_RUN_TESTS: Whether to run tests.
# KUBE_RELEASE_RUN_TESTS: 테스트 실행 여부

#   Set to 'y' to run tests anyways.
# 'y'로 설정하면 어쨌든 테스트 실행

#   KUBE_FASTBUILD: Whether to cross-compile for other architectures.
# KUBE_FASTBUILD: 다른 아키텍처용 크로스 컴파일 여부

#   Set to 'false' to do so.
# 'false'로 설정하면 크로스 컴파일 실행

#   KUBE_DOCKER_REGISTRY: Registry of released images, default to registry.k8s.io
# KUBE_DOCKER_REGISTRY: 릴리스 이미지 레지스트리, 기본값 registry.k8s.io

#   KUBE_BASE_IMAGE_REGISTRY: Registry of base images for controlplane binaries
# KUBE_BASE_IMAGE_REGISTRY: 컨트롤 플레인 바이너리용 베이스 이미지 레지스트리

#   default to registry.k8s.io/build-image
# 기본값 registry.k8s.io/build-image

# Example:
# 예제:

#   make release-skip-tests
# make release-skip-tests

#   make quick-release
# make quick-release
endef

.PHONY: release-skip-tests quick-release
# release-skip-tests와 quick-release 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
release-skip-tests quick-release:
	echo "$RELEASE_SKIP_TESTS_HELP_INFO"
	# release-skip-tests 도움말 출력
else
# 그렇지 않으면
release-skip-tests quick-release: KUBE_RELEASE_RUN_TESTS = n
# 두 타겟 모두 KUBE_RELEASE_RUN_TESTS를 n으로 설정 (테스트 건너뜀)

release-skip-tests quick-release: KUBE_FASTBUILD = true
# 두 타겟 모두 KUBE_FASTBUILD를 true로 설정 (빠른 빌드)

release-skip-tests quick-release:
	build/release.sh
	# 릴리스 스크립트 실행
endif

define QUICK_RELEASE_IMAGES_HELP_INFO
# QUICK_RELEASE_IMAGES 타겟 도움말 정보
# Build release images, but only for linux/amd64
# 릴리스 이미지 빌드하되 linux/amd64용만

# Args:
# 인수들:

#   KUBE_FASTBUILD: Whether to cross-compile for other architectures.
# KUBE_FASTBUILD: 다른 아키텍처용 크로스 컴파일 여부

#   Set to 'false' to do so.
# 'false'로 설정하면 크로스 컴파일 실행

#   KUBE_BUILD_CONFORMANCE: Whether to build conformance testing image as well.
# KUBE_BUILD_CONFORMANCE: 컨포먼스 테스트 이미지도 빌드할지 여부

#   Set to 'y' to do so.
# 'y'로 설정하면 빌드함

#   DBG: If set to "1", build with optimizations disabled for easier debugging.
# DBG: "1"로 설정하면 디버깅을 위해 최적화 비활성화

#   Any other value is ignored.
# 다른 값은 무시됨

# Example:
# 예제:

#   make quick-release-images
# make quick-release-images

#   make quick-release-images DBG=1
# make quick-release-images DBG=1

#     Note: Specify DBG=1 for building unstripped binaries
# 참고: DBG=1을 지정하면 스트립되지 않은 바이너리 빌드

#           which allows you to use code debugging tools like delve.
# 이렇게 하면 delve 같은 코드 디버깅 도구 사용 가능

#           When DBG is unspecified, it defaults to "-s -w" which strips debug information.
# DBG가 지정되지 않으면 기본값 "-s -w"로 디버그 정보 제거
endef

.PHONY: quick-release-images
# quick-release-images 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
quick-release-images:
	echo "$QUICK_RELEASE_IMAGES_HELP_INFO"
	# quick-release-images 도움말 출력
else
# 그렇지 않으면
quick-release-images: KUBE_FASTBUILD = true
# KUBE_FASTBUILD를 true로 설정 (빠른 빌드, amd64만)

quick-release-images:
	build/release-images.sh
	# 릴리스 이미지 빌드 스크립트 실행
endif

define PACKAGE_HELP_INFO
# PACKAGE 타겟 도움말 정보
# Package tarballs
# tarball 패키징

# Use the 'package-tarballs' target to run the final packaging steps of
# 'package-tarballs' 타겟을 사용하여 릴리스의 최종 패키징 단계 실행

# a release.
# 릴리스의

# Example:
# 예제:
#   make package-tarballs
# make package-tarballs
endef

.PHONY: package package-tarballs
# package와 package-tarballs 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
package package-tarballs:
	echo "$PACKAGE_HELP_INFO"
	# package 도움말 출력
else
# 그렇지 않으면
package-tarballs: KUBE_BUILD_CONFORMANCE = y
# package-tarballs 타겟에 KUBE_BUILD_CONFORMANCE를 y로 설정

package package-tarballs:
	build/package-tarballs.sh
	# 패키지 tarball 스크립트 실행
endif

define CROSS_HELP_INFO
# CROSS 타겟 도움말 정보
# Cross-compile for all platforms
# 모든 플랫폼용 크로스 컴파일

# Use the 'cross-in-a-container' target to cross build when already in
# 이미 컨테이너 안에 있을 때 크로스 빌드하려면 'cross-in-a-container' 타겟 사용

# a container vs. creating a new container to build from (build-image)
# (build-image)에서 새 컨테이너를 만들어 빌드하는 것과 대조

# Useful for running in GCB.
# GCB에서 실행할 때 유용함

# Example:
# 예제:

#   make cross
# make cross

#   make cross-in-a-container
# make cross-in-a-container
endef

.PHONY: cross cross-in-a-container
# cross와 cross-in-a-container 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
cross cross-in-a-container:
	echo "$CROSS_HELP_INFO"
	# cross 도움말 출력
else
# 그렇지 않으면
cross cross-in-a-container: KUBE_BUILD_CONFORMANCE = y
# 두 타겟 모두 KUBE_BUILD_CONFORMANCE를 y로 설정

cross:
	hack/make-rules/cross.sh
	# 크로스 컴파일 스크립트 실행

cross-in-a-container: KUBE_OUTPUT_SUBPATH = $(OUT_DIR)/dockerized
# cross-in-a-container 타겟에 출력 서브경로를 dockerized로 설정

cross-in-a-container:
ifeq (,$(wildcard /.dockerenv))
# /.dockerenv 파일이 존재하지 않으면 (컨테이너 밖에 있으면)
	echo -e "\nThe 'cross-in-a-container' target can only be used from within a docker container.\n"
	# 에러 메시지 출력: 컨테이너 안에서만 사용 가능
else
# 컨테이너 안에 있으면
	hack/make-rules/cross.sh
	# 크로스 컴파일 스크립트 실행
endif
endif

define CMD_HELP_INFO
# CMD 타겟 도움말 정보
# Add rules for all directories in cmd/
# cmd/ 디렉토리의 모든 디렉토리에 대한 규칙 추가

# Example:
# 예제:
#   make kubectl kube-proxy
# make kubectl kube-proxy
endef

#TODO: make EXCLUDE_TARGET auto-generated when there are other files in cmd/
# TODO: cmd/에 다른 파일들이 있을 때 EXCLUDE_TARGET을 자동 생성하도록 만들기

EXCLUDE_TARGET=OWNERS
# 제외할 타겟 설정 (OWNERS 파일)

CMD_TARGET = $(filter-out %$(EXCLUDE_TARGET),$(notdir $(abspath $(wildcard cmd/*/))))
# cmd/ 디렉토리의 모든 서브디렉토리에서 EXCLUDE_TARGET을 제외한 것들을 CMD_TARGET으로 설정

.PHONY: $(CMD_TARGET)
# CMD_TARGET에 있는 모든 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
$(CMD_TARGET):
	echo "$CMD_HELP_INFO"
	# CMD 도움말 출력
else
# 그렇지 않으면
$(CMD_TARGET):
	hack/make-rules/build.sh cmd/$@
	# 해당 cmd 하위 디렉토리를 빌드 ($@는 타겟 이름)
endif

define HELP_INFO
# HELP 타겟 도움말 정보
# Print make targets and help info
# make 타겟들과 도움말 정보 출력

# Example:
# 예제:
# make help
# make help
endef

.PHONY: help
# help 타겟을 phony 타겟으로 선언

ifeq ($(PRINT_HELP),y)
# 도움말 모드이면
help:
	echo "$HELP_INFO"
	# help 도움말 출력
else
# 그렇지 않으면
help:
	hack/make-rules/make-help.sh
	# 도움말 생성 스크립트 실행
endif

## 주요 구조 설명

### 변수 설정 부분
#- **환경 변수**: `OUT_DIR`, `BIN_DIR`, `GOFLAGS` 등 빌드에 필요한 변수들 정의
#- **조건부 설정**: `ifdef`, `ifeq` 등을 사용한 조건부 변수 설정
#- **Make 플래그**: `--no-builtin-rules`, `--warn-undefined-variables` 등으로 Make 동작 제어
#
#### 도움말 시스템
#- **`define ... endef`**: 각 타겟별 상세한 도움말 정보 정의
#- **`PRINT_HELP` 변수**: 도움말 출력 모드와 실제 실행 모드 구분
#- **조건부 실행**: `ifeq ($(PRINT_HELP),y)` 로 모드 분기
#
#### 주요 타겟들
#- **빌드**: `all`, `ginkgo`, `cross` - 코드 컴파일
#- **테스트**: `test`, `test-integration`, `test-e2e-node` - 다양한 레벨의 테스트
#- **검증**: `verify`, `quick-verify`, `vet`, `lint` - 코드 품질 검사
#- **릴리스**: `release`, `release-images`, `package` - 배포용 빌드
#- **정리**: `clean` - 빌드 아티팩트 제거
#
#### 의존성 관리
#- **Phony 타겟**: `.PHONY` 선언으로 파일이 아닌 명령임을 명시
#- **타겟 의존성**: `test-e2e-node: ginkgo` 같은 의존 관계 정의
#- **스크립트 호출**: 실제 작업은 `hack/` 디렉토리의 스크립트들이 담당