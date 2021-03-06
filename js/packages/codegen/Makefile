export PWD := $(patsubst %/,%,$(dir $(abspath $(lastword $(MAKEFILE_LIST)))))
export PATH := $(PWD)/node_modules/.bin:$(PATH)

berty_root := $(abspath $(PWD)/../../..)
berty_go_path := $(abspath $(PWD)/../../../go)
berty_api_path := $(abspath $(PWD)/../../../api)

codegen_mod := $(PWD)/node_modules
codegen_bin := $(codegen_mod)/.bin

codegen_lint := $(codegen_bin)/eslint
codegen_lint_options := --cache --fix --no-ignore

codegen_berty_path := $(berty_api_path)
codegen_protobuf_path := $(abspath $(PWD)/../../node_modules/@protocolbuffers/protobuf/src)
codegen_googleapis_path := $(abspath $(PWD)/../../node_modules/@googleapis/googleapis)
codegen_go_path := $(abspath $(berty_root)/vendor)

codegen_berty_protocol_protos := \
	$(codegen_berty_path)/bertyprotocol.proto \

codegen_berty_messenger_protos := \
	$(codegen_berty_path)/bertymessenger.proto \

codegen_berty_types_protos := \
	$(codegen_berty_path)/bertytypes.proto \

codegen_berty_shared_protos := \
	$(codegen_berty_protocol_protos) \
	$(codegen_berty_messenger_protos) \
	$(codegen_berty_types_protos) \

codegen_berty_protocol_sources := $(shell \
	find $(abspath $(PWD)/..) -type f -name '*.hbs' -path '*/*/protocol/*' -not -path '*/node_modules/*')
codegen_berty_messenger_sources := $(shell \
	find $(abspath $(PWD)/..) -type f -name '*.hbs' -path '*/*/messenger/*' -not -path '*/node_modules/*')
codegen_berty_types_sources := $(shell \
	find $(abspath $(PWD)/..) -type f -name '*.hbs' -path '*/*/types/*' -not -path '*/node_modules/*')
codegen_berty_shared_sources := $(shell \
	find $(abspath $(PWD)/..) -maxdepth 2 -type f -name '*.hbs' -not -path '*/berty-*/*')

codegen_berty_protocol_targets := $(patsubst %.hbs, %, $(codegen_berty_protocol_sources))
codegen_berty_messenger_targets := $(patsubst %.hbs, %, $(codegen_berty_messenger_sources))
codegen_berty_types_targets := $(patsubst %.hbs, %, $(codegen_berty_types_sources))
codegen_berty_shared_targets := $(patsubst %.hbs, %, $(codegen_berty_shared_sources))

codegen_targets := \
	$(codegen_berty_protocol_targets) \
	$(codegen_berty_messenger_targets) \
	$(codegen_berty_types_targets) \
	$(codegen_berty_shared_targets) \

codegen_pbhbs := $(abspath $(PWD)/node_modules/.bin/pbhbs)
codegen_pbhbs_flags := \
	-p $(codegen_berty_path) \
	-p $(codegen_googleapis_path) \
	-p $(codegen_go_path) \
	-p $(codegen_protobuf_path) \
	-H $(PWD)/helpers \

codegen_deps := \
	$(PWD)/Makefile \
	$(codegen_go_path) \
	$(PWD)/node_modules \

$(codegen_berty_protocol_targets): %: %.hbs
	$(codegen_pbhbs) $(codegen_pbhbs_flags) -t $(dir $@) -o $(dir $@) $(codegen_berty_protocol_protos)

$(codegen_berty_messenger_targets): %: %.hbs
	$(codegen_pbhbs) $(codegen_pbhbs_flags) -t $(dir $@) -o $(dir $@) $(codegen_berty_messenger_protos)

$(codegen_berty_types_targets): %: %.hbs
	$(codegen_pbhbs) $(codegen_pbhbs_flags) -t $(dir $@) -o $(dir $@) $(codegen_berty_types_protos)

$(codegen_berty_shared_targets): %: %.hbs
	$(codegen_pbhbs) $(codegen_pbhbs_flags) -t $(dir $@) -o $(dir $@) $(codegen_berty_shared_protos)

.PHONY: gen.codegen
gen.codegen: $(codegen_deps) $(codegen_targets)
	$(codegen_lint) $(codegen_lint_options) $(codegen_targets)

.PHONY: gen.codegen.watch
gen.codegen.watch: export PWD := $(PWD)
gen.codegen.watch: $(codegen_deps) $(codegen_targets)
	watchexec -w $(PWD)/.. -- make gen.codegen

.PHONY: gen
gen: gen.codegen
