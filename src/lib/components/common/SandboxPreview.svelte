<script lang="ts">
	import { getContext } from 'svelte';
	const i18n: Writable<i18nType> = getContext('i18n');

	import { type Writable } from 'svelte/store';
	import type { i18n as i18nType } from 'i18next';
	import JavascriptCodeEditor from '$lib/components/common/JavascriptCodeEditor.svelte';

	import {
		showSidebar,
		showPreview,
		user,
		mobile,
		showPreviewCode,
		showPreviewLive
	} from '$lib/stores';

	import Tooltip from '../common/Tooltip.svelte';
	import Sidebar from '../icons/Sidebar.svelte';
	import PhonePreview from '../chat/PhonePreview.svelte';
	import ClosePreview from '../icons/ClosePreview.svelte';
</script>

<div class="row h-full w-1/2 p-2 {$mobile ? 'w-full' : ''}">
	<div
		class="h-full p-2 bg-white dark:bg-gray-900 rounded-3xl border border-gray-100 dark:border-gray-850"
	>
		<nav class="px-2.5 backdrop-blur-xl drag-region">
			<div class=" flex items-center gap-1">
				{#if $mobile}
					<div class="{$showSidebar ? 'md:hidden' : ''} self-center flex flex-none items-center">
						<Tooltip
							content={$showSidebar ? $i18n.t('Close Sidebar') : $i18n.t('Open Sidebar')}
							interactive={true}
						>
							<button
								id="sidebar-toggle-button"
								class=" cursor-pointer flex rounded-lg hover:bg-gray-100 dark:hover:bg-gray-850 transition cursor-"
								on:click={() => {
									showSidebar.set(!$showSidebar);
								}}
							>
								<div class=" self-center p-1.5">
									<Sidebar />
								</div>
							</button>
						</Tooltip>

						<Tooltip
							content={$showPreview ? $i18n.t('Hide Preview') : $i18n.t('Open Preview')}
							interactive={true}
						>
							<button
								id="sidebar-toggle-button"
								class=" cursor-pointer flex rounded-lg hover:bg-gray-100 dark:hover:bg-gray-850 transition cursor-"
								on:click={() => {
									showPreview.set(!$showPreview);
								}}
							>
								<div class=" self-center p-1.5">
									<ClosePreview />
								</div>
							</button>
						</Tooltip>
					</div>
				{/if}

				<div class="">
					<div
						class="flex gap-1 scrollbar-none overflow-x-auto w-fit text-center text-sm font-medium rounded-full bg-transparent py-1 touch-auto pointer-events-auto"
					>
						{#if $user?.role === 'admin' || $user?.permissions?.workspace?.models}
							<a
								class="min-w-fit p-1.5 {$showPreviewCode
									? ''
									: 'text-gray-300 dark:text-gray-600 hover:text-gray-700 dark:hover:text-white'} transition"
								on:click={() => {
									showPreviewCode.set(true);
									showPreviewLive.set(false);
								}}
								href=""
							>
								{$i18n.t('Code')}
							</a>
						{/if}

						{#if $user?.role === 'admin' || $user?.permissions?.workspace?.models}
							<a
								class="min-w-fit p-1.5 {$showPreviewLive
									? ''
									: 'text-gray-300 dark:text-gray-600 hover:text-gray-700 dark:hover:text-white'} transition"
								on:click={() => {
									showPreviewCode.set(false);
									showPreviewLive.set(true);
								}}
								href=""
							>
								{$i18n.t('Preview')}
							</a>
						{/if}
					</div>
				</div>

				<!-- <div class="flex items-center text-xl font-semibold">{$i18n.t('Workspace')}</div> -->
			</div>
		</nav>
		{#if $showPreviewCode}
			<JavascriptCodeEditor lang="JavaScript" id="JavaScript"></JavascriptCodeEditor>
		{/if}
		{#if $showPreviewLive}
			<PhonePreview></PhonePreview>
		{/if}
	</div>
</div>
