<template lang="pug">
	section.vfg-group
		template(v-for='group in groups' v-if="fieldVisible(group)")
			drawer(v-if="groupFoldable(group)", :group="group", :model="model")
				fieldset(slot="title")
					legend {{ group.legend }}
				template(slot="content")
					fieldset(:is='tag', :id="group.id")
					template(v-if="group.fields")
						template(v-for='field in group.fields')
							form-field(:vfg="vfg", :field="field", :errors="errors", :model="model", :options="options", @validated="onFieldValidated", @model-updated="onModelUpdated")
					form-group(:class="getFieldRowClasses(group)", v-if="group.groups", :options="options", :tag="tag", :groups="group.groups", :model="model", :vfg="vfg", @validated="onFieldValidated", @model-updated="onModelUpdated")
			template(v-else)
				fieldset(:is='tag', :class='getFieldRowClasses(group)', :id="group.id")
					legend(v-if='group.legend') {{ group.legend }}
					template(v-if="group.fields")
						template(v-for='field in group.fields')
							form-field(:vfg="vfg", :field="field", :errors="errors", :model="model", :options="options", @validated="onFieldValidated", @model-updated="onModelUpdated")
					form-group(:class="getFieldRowClasses(group)", v-if="group.groups",:options="options", :tag="tag", :groups="group.groups", :model="model", :vfg="vfg", @validated="onFieldValidated", @model-updated="onModelUpdated")
</template>

<script>
import { get as isFunction, isNil } from "lodash";
import drawer from "./drawer";
import formMixin from "./formMixin.js";
import formField from "./formField.vue";

export default {
	name: "form-group",
	components: { drawer, formField },
	mixins: [formMixin],
	props: {
		vfg: Object,
		model: Object,
		groups: Array,
		tag: String,
		options: Object
	},
	data() {
		return {
			errors: [] // Validation errors
		};
	},
	methods: {
		groupFoldable(group) {
			if (isFunction(group.foldable)) return group.foldable.call(this, this.model, group, this);

			if (isNil(group.foldable)) return false;

			return group.foldable;
		},
		onFieldValidated(value) {
			this.$emit("onFieldValidated", value);
		},
		onModelUpdated(value) {
			this.$emit("onModelUpdated", value);
		}
	}
};
</script>

<style lang="scss"></style>
