<script setup lang="ts">
import type { Field, ValidationError } from '@directus/types';
import { useElementSize } from '@vueuse/core'
import { computed, ref, unref, useTemplateRef } from 'vue';
import TabButton from './tab-button.vue';

interface Tab extends Field {
	index: number;
}

const props = defineProps<{
	modelValue: number;
	tabFields: Field[];
	fields: Field[];
	values: Record<string, unknown>;
	validationErrors: ValidationError[];
	batchMode?: boolean;
}>();

defineEmits<{
	(e: 'update:modelValue', value: number): void;
}>();

const buttons = ref([]);
const tabListWidth = useTemplateRef('tabListWidth');
const { width } = useElementSize(tabListWidth);
const visibleTabs = computed(() => Math.floor(width.value / 124));

// we can't use a computed here because document.activeElement isn't reactive
function getFocusIndex() {
	// @ts-ignore
	const index = buttons.value.findIndex((button) => button.$el.contains(document.activeElement));
	return index !== -1 ? index : props.modelValue;
}

function setFocusIndex(index: number) {
	if(!buttons.value[index]) return;
	// @ts-ignore
	buttons.value[index].$el.querySelector('button').focus();
}

function onKeyDown(event: KeyboardEvent) {
	const focusIndex = getFocusIndex();
	switch (event.key) {
		case 'ArrowLeft':
			if (focusIndex !== -1) {
				setFocusIndex((unref(buttons).length + focusIndex - 1) % unref(buttons).length);
			}
			break;
		case 'ArrowRight':
			if (focusIndex !== -1) {
				setFocusIndex((focusIndex + 1) % unref(buttons).length);
			}
			break;
	}
}

const visibleFields = computed<Tab[]>(() => {
	let tabs: any[] = props.tabFields
		.map((t, i) => {
			return { ...t, index: i };
		})
		.slice(0, visibleTabs.value);
	if(props.modelValue && !(props.modelValue in tabs)){
		tabs.splice(visibleTabs.value - 1, 1, { ...props.tabFields[props.modelValue], index: props.modelValue });
	}
	return tabs;
});
</script>

<template>
	<div>
		<div class="tab-container">
			<div role="tablist" ref="tabListWidth" class="tab-list" @keydown="onKeyDown">
				<tab-button
					v-for="field in visibleFields"
					ref="buttons"
					:key="field.field"
					:field="field"
					:fields="fields"
					:values="values"
					:validation-errors="validationErrors"
					:active="field.index === modelValue"
					:batch-mode="batchMode"
					@click="$emit('update:modelValue', field.index)"
				/>
			</div>
			<div class="tab-selector" v-if="tabFields.length > visibleTabs">
				<v-menu>
					<template #activator="{ toggle }">
						<v-button class="more-tabs" icon @click="toggle" small>
							<v-icon	name="arrow_drop_down" />
						</v-button>
					</template>
					<v-list>
						<v-list-item
							clickable
							v-for="(field, index) in tabFields"
							:class="{
								'tab-menu-item-hidden': visibleFields.some((t) => t.index === index)
							}"
							:key="field.field"
							@click="$emit('update:modelValue', index)"
						>
							<span class="field-name">{{ field.name }}</span>
						</v-list-item>
					</v-list>
				</v-menu>
			</div>
		</div>
		<slot />
	</div>
</template>

<style scoped lang="css">
.tab-container {
	display: flex;
	justify-content: space-between;
	border-radius: calc(var(--theme--border-radius) + 2px);
	background-color: var(--theme--background-subdued);
}
.tab-list {
	display: flex;
	width: 100%;
	gap: 0 0.5rem;
	padding: 2px;
	flex-wrap: wrap;
	overflow: hidden;
	height: calc(var(--v-button-height, 36px) + 4px);
}
.tab-selector {
	display: flex;
	padding: 2px;
}
.more-tabs {
	margin-left: 2px;
	--v-button-border-color: var(--theme--background-accent);
	--v-button-background-color: var(--theme--background-accent);
	--v-button-background-color-hover: var(--theme--background-accent);
}

.more-tabs .v-icon {
	color: var(--theme--foreground)
}
</style>
<style lang="css">
.v-list .tab-menu-item-hidden {
	display: none;
}
</style>