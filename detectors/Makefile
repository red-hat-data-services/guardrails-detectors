RHOAI_VERSION=3.5
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

UV_COMPILE_FLAGS=--python-version 3.12 --python-platform linux \
	--no-emit-package guardrails-detectors \
	--index-strategy first-index --emit-index-url --emit-index-annotation \
	--generate-hashes --prerelease=if-necessary --no-strip-markers

.PHONY: hf-cpu-requirements hf-cuda-requirements all-requirements check-indexes

hf-cpu-requirements:
	uv pip compile pyproject.toml --extra huggingface \
		$(UV_COMPILE_FLAGS) \
		--default-index $(RHAI_CPU_INDEX_URL) \
		-o requirements-hf/cpu/requirements.txt

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

all-requirements: hf-cpu-requirements hf-cuda-requirements built-in-requirements

check-indexes:
	@echo "CPU index: $(RHAI_CPU_INDEX_URL)"
	@echo "CUDA index: $(RHAI_CUDA_INDEX_URL)"
