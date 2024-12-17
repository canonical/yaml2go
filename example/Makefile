default: help

CILIUM_FILE_NAME := cilium-1.16.3
RAWFILE_CSI_FILE_NAME := rawfile-csi-0.9.0
GO_PACKAGE_NAME := autogenvalues
TARGET_DIR := ./out/${GO_PACKAGE_NAME}

.PHONY: help
help:
	@echo "Available targets:"
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | awk 'BEGIN {FS = ":.*?## "}; {printf "  %-15s %s\n", $$1, $$2}'

.PHONY: extract-values
extract-values: ## Extract values.yaml files from Helm charts.
	@echo "Extracting Cilium values..."
	@tar --transform='s|cilium/values.yaml|${CILIUM_FILE_NAME}_values.yaml|' -zxf ${CILIUM_FILE_NAME}.tgz cilium/values.yaml

	@echo "Extracting Rawfile CSI values..."
	@tar --transform='s|rawfile-csi/values.yaml|${RAWFILE_CSI_FILE_NAME}_values.yaml|' -zxf ${RAWFILE_CSI_FILE_NAME}.tgz rawfile-csi/values.yaml

.PHONY: gen
gen: extract-values ## Generate Go code from values.yaml files.
	@go run generator.go -files=${CILIUM_FILE_NAME}_values.yaml,${RAWFILE_CSI_FILE_NAME}_values.yaml -pkg=${GO_PACKAGE_NAME} -out-dir=${TARGET_DIR} -advanced-types=true -unsafe-field=true

.PHONY: clean
clean: clean-yaml-values clean-gen-values ## Clean up.

.PHONY: clean-yaml-values
clean-yaml-values: ## Clean up extracted values.yaml files.
	rm -f ${CILIUM_FILE_NAME}_values.yaml
	rm -f ${RAWFILE_CSI_FILE_NAME}_values.yaml

.PHONY: clean-gen-values
clean-gen-values: ## Clean up generated Go code.
	rm -f ${TARGET_DIR}/${CILIUM_FILE_NAME}_values.go
	rm -f ${TARGET_DIR}/${RAWFILE_CSI_FILE_NAME}_values.go

