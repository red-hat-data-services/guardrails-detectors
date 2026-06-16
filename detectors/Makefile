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

.PHONY: hf-cpu-requirements hf-cuda-requirements common-cpu-requirements common-cuda-requirements hf-requirements common-requirements all-requirements check-indexes

hf-cpu-requirements:
	uv pip compile --python-platform linux --emit-index-url --default-index $(RHAI_CPU_INDEX_URL) ./huggingface/cpu/requirements.txt --index-url $(RHAI_CPU_INDEX_URL) -o ./huggingface/cpu/requirements.txt

hf-cuda-requirements:
	uv pip compile --python-platform linux --emit-index-url --default-index $(RHAI_CUDA_INDEX_URL) ./huggingface/cuda/requirements.txt --index-url $(RHAI_CUDA_INDEX_URL) -o ./huggingface/cuda/requirements.txt

common-cpu-requirements:
	uv pip compile --python-platform linux --emit-index-url --default-index $(RHAI_CPU_INDEX_URL) ./common/cpu/requirements.txt --index-url $(RHAI_CPU_INDEX_URL) -o ./common/cpu/requirements.txt

common-cuda-requirements:
	uv pip compile --python-platform linux --emit-index-url --default-index $(RHAI_CUDA_INDEX_URL) ./common/cuda/requirements.txt --index-url $(RHAI_CUDA_INDEX_URL) -o ./common/cuda/requirements.txt

hf-requirements: hf-cpu-requirements hf-cuda-requirements

common-requirements: common-cpu-requirements common-cuda-requirements

all-requirements: hf-requirements common-requirements

check-indexes:
	@echo "CPU index: $(RHAI_CPU_INDEX_URL)"
	@echo "CUDA index: $(RHAI_CUDA_INDEX_URL)"