<template>
<x-container @remove="() => $emit('remove')" :draggable="true">
	<template #header><fa :icon="faBolt"/> {{ $t('_pages.blocks.switch') }}</template>

	<section class="kjuadyyj">
		<mk-input v-model="value.name"><template #prefix><fa :icon="faMagic"/></template><span>{{ $t('_pages.blocks._switch.name') }}</span></mk-input>
		<mk-input v-model="value.text"><span>{{ $t('_pages.blocks._switch.text') }}</span></mk-input>
		<mk-switch v-model="value.default"><span>{{ $t('_pages.blocks._switch.default') }}</span></mk-switch>
	</section>
</x-container>
</template>

<script lang="ts">
import Vue from 'vue';
import { faBolt, faMagic } from '@fortawesome/free-solid-svg-icons';
import i18n from '../../../i18n';
import XContainer from '../page-editor.container.vue';
import MkSwitch from '../../../components/ui/switch.vue';
import MkInput from '../../../components/ui/input.vue';

export default Vue.extend({
	i18n,

	components: {
		XContainer, MkSwitch, MkInput
	},

	props: {
		value: {
			required: true
		},
	},

	data() {
		return {
			faBolt, faMagic
		};
	},

	created() {
		if (this.value.name == null) Vue.set(this.value, 'name', '');
	},
});
</script>

<style lang="scss" scoped>
.kjuadyyj {
	padding: 0 16px 16px 16px;
}
</style>
