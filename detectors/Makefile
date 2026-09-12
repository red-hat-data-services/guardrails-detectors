RHOAI_VERSION=3.6-EA2
CUDA_VERSION=13.0
UBI_VERSION=9

RHAI_CPU_PROD_URL=https://packages.redhat.com/api/pypi/public-rhai/rhoai/$(RHOAI_VERSION)/cpu-ubi$(UBI_VERSION)/simple/
RHAI_CPU_TEST_URL=https://packages.redhat.com/api/pypi/public-rhai/rhoai/$(RHOAI_VERSION)/cpu-ubi$(UBI_VERSION)-test/simple/
RHAI_CUDA_PROD_URL=https://packages.redhat.com/api/pypi/public-rhai/rhoai/$(RHOAI_VERSION)/cuda$(CUDA_VERSION)-ubi$(UBI_VERSION)/simple/
RHAI_CUDA_TEST_URL=https://packages.redhat.com/api/pypi/public-rhai/rhoai/$(RHOAI_VERSION)/cuda$(CUDA_VERSION)-ubi$(UBI_VERSION)-test/simple/

# Resolve index URL: use prod if available, fall back to test
define resolve_index_url
$(shell if curl -sf -o /dev/null "$(1)"; then echo "$(1)"; else echo "$(2)"; fi)
endef

RHAI_CPU_INDEX_URL=$(call resolve_index_url,$(RHAI_CPU_PROD_URL),$(RHAI_CPU_TEST_URL))
RHAI_CUDA_INDEX_URL=$(call resolve_index_url,$(RHAI_CUDA_PROD_URL),$(RHAI_CUDA_TEST_URL))

UV_COMMON_FLAGS=--python-version 3.12 \
	--index-strategy first-index --emit-index-url --emit-index-annotation \
	--generate-hashes --prerelease=if-necessary --no-strip-markers

UV_COMPILE_FLAGS=$(UV_COMMON_FLAGS) --python-platform linux \
	--no-emit-package guardrails-detectors

.PHONY: hf-cpu-requirements hf-cuda-requirements build-requirements all-requirements check-indexes

hf-cpu-requirements:
	uv pip compile pyproject.toml --extra huggingface \
		$(UV_COMPILE_FLAGS) \
		--default-index $(RHAI_CPU_INDEX_URL) \
		-o requirements-hf/cpu/requirements-full.txt
	python3 scripts/extract_native_requirements.py \
		requirements-hf/cpu/requirements-full.txt \
		requirements-hf/cpu/requirements.txt

hf-cuda-requirements:
	uv pip compile pyproject.toml --extra huggingface \
		$(UV_COMPILE_FLAGS) \
		--default-index $(RHAI_CUDA_INDEX_URL) \
		-o requirements-hf/cuda/requirements.txt

built-in-requirements:
	uv pip compile pyproject.toml --extra built-in \
		$(UV_COMPILE_FLAGS) \
		--default-index $(RHAI_CPU_INDEX_URL) \
		-o requirements.builtIn.txt

build-requirements:
	echo "setuptools>=80.9.0" | uv pip compile - \
		$(UV_COMMON_FLAGS) --python-platform x86_64-manylinux_2_34 \
		--default-index $(RHAI_CPU_INDEX_URL) \
		-o requirements-build.txt

all-requirements: hf-cpu-requirements hf-cuda-requirements built-in-requirements build-requirements

check-indexes:
	@echo "CPU index: $(RHAI_CPU_INDEX_URL)"
	@echo "CUDA index: $(RHAI_CUDA_INDEX_URL)"
