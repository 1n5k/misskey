<template>
<section class="_card">
	<div class="_title"><fa :icon="faCog"/> {{ $t('general') }}</div>
	<div class="_content">
		<mk-input type="file" @change="onWallpaperChange" style="margin-top: 0;">
			<span>{{ $t('wallpaper') }}</span>
			<template #icon><fa :icon="faImage"/></template>
			<template #desc v-if="wallpaperUploading">{{ $t('uploading') }}<mk-ellipsis/></template>
		</mk-input>
		<mk-button primary :disabled="$store.state.settings.wallpaper == null" @click="delWallpaper()">{{ $t('removeWallpaper') }}</mk-button>
	</div>
	<div class="_content">
		<mk-switch v-model="$store.state.i.autoWatch" @change="onChangeAutoWatch">
			{{ $t('auto-watch') }}<template #desc>{{ $t('auto-watch-desc') }}</template>
		</mk-switch>
	</div>
	<div class="_content">
		<mk-button @click="readAllNotifications">{{ $t('mark-as-read-all-notifications') }}</mk-button>
		<mk-button @click="readAllUnreadNotes">{{ $t('mark-as-read-all-unread-notes') }}</mk-button>
		<mk-button @click="readAllMessagingMessages">{{ $t('mark-as-read-all-talk-messages') }}</mk-button>
	</div>
</section>
</template>

<script lang="ts">
import Vue from 'vue';
import { faImage, faCog } from '@fortawesome/free-solid-svg-icons';
import MkInput from '../../components/ui/input.vue';
import MkButton from '../../components/ui/button.vue';
import MkSwitch from '../../components/ui/switch.vue';
import i18n from '../../i18n';
import { apiUrl } from '../../config';

export default Vue.extend({
	i18n,

	components: {
		MkInput,
		MkButton,
		MkSwitch,
	},
	
	data() {
		return {
			wallpaperUploading: false,
			faImage, faCog
		}
	},

	computed: {
		wallpaper: {
			get() { return this.$store.state.settings.wallpaper; },
			set(value) { this.$store.dispatch('settings/set', { key: 'wallpaper', value }); }
		},
	},

	methods: {
		onWallpaperChange([file]) {
			this.wallpaperUploading = true;

			const data = new FormData();
			data.append('file', file);
			data.append('i', this.$store.state.i.token);

			fetch(apiUrl + '/drive/files/create', {
				method: 'POST',
				body: data
			})
			.then(response => response.json())
			.then(f => {
				this.wallpaper = f.url;
				this.wallpaperUploading = false;
				document.documentElement.style.backgroundImage = `url(${this.$store.state.settings.wallpaper})`;
			})
			.catch(e => {
				this.wallpaperUploading = false;
				this.$root.dialog({
					type: 'error',
					text: e
				});
			});
		},

		delWallpaper() {
			this.wallpaper = null;
			document.documentElement.style.backgroundImage = 'none';
		},

		onChangeAutoWatch(v) {
			this.$root.api('i/update', {
				autoWatch: v
			});
		},

		readAllUnreadNotes() {
			this.$root.api('i/read_all_unread_notes');
		},

		readAllMessagingMessages() {
			this.$root.api('i/read_all_messaging_messages');
		},

		readAllNotifications() {
			this.$root.api('notifications/mark_all_as_read');
		}
	}
});
</script>
