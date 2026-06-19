<script lang="ts" setup>
import KeyboardShortcutTooltip from '@/app/components/KeyboardShortcutTooltip.vue';
import { useI18n } from '@n8n/i18n';
import { computed } from 'vue';
import type { ButtonVariant } from '@n8n/design-system';

import { N8nButton } from '@n8n/design-system';

// Explicit click emit is required for Playwright tests - N8nTooltip wraps
// content in a span (via TooltipTrigger), which can interfere with click
// event detection in automated tests
const emit = defineEmits<{
	click: [];
}>();

const props = withDefaults(
	defineProps<{
		saved: boolean;
		isSaving?: boolean;
		disabled?: boolean;
		variant?: ButtonVariant;
		withShortcut?: boolean;
		shortcutTooltip?: string;
		savingLabel?: string;
	}>(),
	{
		isSaving: false,
		variant: 'solid',
		withShortcut: false,
		disabled: false,
	},
);

const i18n = useI18n();

const saveButtonLabel = computed(() => {
	return props.isSaving
		? (props.savingLabel ?? i18n.baseText('saveButton.saving'))
		: i18n.baseText('saveButton.save');
});

const shortcutTooltipLabel = computed(() => {
	return props.shortcutTooltip ?? i18n.baseText('saveButton.save');
});
</script>

<template>
	<span :class="$style.container" data-test-id="save-button">
		<Transition name="save-state" mode="out-in">
			<span v-if="saved" :class="$style.saved" key="saved" @click.prevent.stop>{{
				i18n.baseText('saveButton.saved')
			}}</span>
			<KeyboardShortcutTooltip
				v-else-if="withShortcut"
				key="save-shortcut"
				:label="shortcutTooltipLabel"
				:shortcut="{ keys: ['s'], metaKey: true }"
				placement="bottom"
			>
				<N8nButton
					:label="saveButtonLabel"
					:loading="isSaving"
					:disabled="disabled"
					:variant="variant"
					@click="emit('click')"
				/>
			</KeyboardShortcutTooltip>
			<N8nButton
				v-else
				key="save"
				:label="saveButtonLabel"
				:loading="isSaving"
				:disabled="disabled"
				:variant="variant"
				@click="emit('click')"
			/>
		</Transition>
	</span>
</template>

<style lang="scss" module>
.container {
	display: inline-flex;
	justify-content: center;
	align-items: center;
	min-width: 53px;
}

.saved {
	color: $custom-font-very-light;
	font-size: 12px;
	font-weight: var(--font-weight--bold);
	line-height: 12px;
	text-align: center;
	padding: var(--spacing--2xs) var(--spacing--2xs);
	min-width: 53px;
}

:global(.save-state-enter-active),
:global(.save-state-leave-active) {
	transition: opacity 0.15s ease;
}

:global(.save-state-enter-from),
:global(.save-state-leave-to) {
	opacity: 0;
}
</style>
