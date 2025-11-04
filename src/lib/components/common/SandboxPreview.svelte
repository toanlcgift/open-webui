<script lang="ts">
	import { getContext, onMount } from 'svelte';
	const i18n: Writable<i18nType> = getContext('i18n');

	import { type Writable } from 'svelte/store';
	import type { i18n as i18nType } from 'i18next';
	import JavascriptCodeEditor from '$lib/components/common/JavascriptCodeEditor.svelte';
	import { toast } from 'svelte-sonner';

	import {
		showSidebar,
		showPreview,
		user,
		mobile,
		showPreviewCode,
		showPreviewLive,
		showConsoleLog,
		currentPreviewCode,
		currentConsoleLog,
		socket,
		chatId,
		settings,
		models,
		currentSelectedModel
	} from '$lib/stores';

	import Tooltip from '../common/Tooltip.svelte';
	import Sidebar from '../icons/Sidebar.svelte';
	import PhonePreview from '../chat/PhonePreview.svelte';
	import ClosePreview from '../icons/ClosePreview.svelte';
	import Clip from '../icons/Clip.svelte';
	import BookOpen from '../icons/BookOpen.svelte';
	import Exploit from '../icons/Exploit.svelte';
	import { OPENAI_API_V1_BASE_URL } from '$lib/constants';
	import Switch from '$lib/components/common/Switch.svelte';

	let applications: Array<{ pid: string; name: string; identifier: string }> = [];
	let currentApplication: string = '';
	let forceRunning = true;

	const onExploit = async () => {
		if (!$settings.phoneIP) {
			toast.error($i18n.t('Please set the Phone IP address in Settings first.'));
			return;
		}

		console.log('current Selected model: ' + $currentSelectedModel);
		const res = await fetch(`${OPENAI_API_V1_BASE_URL}/v1/exploit`, {
			method: 'POST',
			headers: {
				Accept: 'application/json',
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				Code: $currentPreviewCode,
				ModelId: $currentSelectedModel,
				PhoneIP: $settings.phoneIP,
				AppName: forceRunning
					? `-f ${currentApplication}`
					: applications.filter(
							(app) => (app['identifier'] ?? app['name']) === currentApplication
						)[0]?.name
			})
		})
			.then(async (res) => {
				if (!res.ok) throw await res.json();
				var text = await res.text();

				showPreviewCode.set(false);
				showPreviewLive.set(true);
				showConsoleLog.set(false);
				currentConsoleLog.set(text);
			})
			.catch((err) => {
				toast.error(err.toString());
				currentConsoleLog.set(err.toString());
				if ('detail' in err) {
				} else {
				}
				return null;
			});
	};

	const onLoadApplications = async () => {
		if (!$settings.phoneIP) {
			toast.error($i18n.t('Please set the Phone IP address in Settings first.'));
			return;
		}

		const res = await fetch(`${OPENAI_API_V1_BASE_URL}/v1/process_list`, {
			method: 'POST',
			headers: {
				Accept: 'application/json',
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({
				PhoneIP: $settings.phoneIP
			})
		})
			.then(async (res) => {
				if (!res.ok) throw await res.json();
				applications = await res.json();
			})
			.catch((err) => {
				toast.error(err.toString());
				currentConsoleLog.set(err.toString());
				if ('detail' in err) {
				} else {
				}
				applications = [
					{
						pid: 'Zer0Cy Application',
						name: 'Zer0Cy Application',
						identifier: 'Zer0Cy Application'
					}
				];
				return null;
			});
	};

	onMount(async () => {
		await onLoadApplications();
	});
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
								showConsoleLog.set(false);
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
								showConsoleLog.set(false);
							}}
							href=""
						>
							{$i18n.t('Preview')}
						</a>
					{/if}

					{#if $user?.role === 'admin' || $user?.permissions?.workspace?.models}
						<a
							class="min-w-fit p-1.5 {$showConsoleLog
								? ''
								: 'text-gray-300 dark:text-gray-600 hover:text-gray-700 dark:hover:text-white'} transition"
							on:click={() => {
								showPreviewCode.set(false);
								showPreviewLive.set(false);
								showConsoleLog.set(true);
							}}
							href=""
						>
							{$i18n.t('Console')}
						</a>
					{/if}
				</div>

				<div class="flex items-center relative">
					<select
						class="dark:bg-gray-900 w-fit pr-8 rounded-sm py-2 px-2 text-xs bg-transparent text-right {$settings.highContrastMode
							? ''
							: 'outline-hidden'}"
						bind:value={currentApplication}
						placeholder={$i18n.t('Select an application')}
						on:change={(e) => {}}
					>
						{#each applications as application}
							<option value={application['identifier'] ?? application['name']}
								>{application['identifier'] ?? application['name']}</option
							>
						{/each}
					</select>

					<Switch
						ariaLabelledbyId="use-chat-title-as-tab-title-label"
						tooltip={true}
						bind:state={forceRunning}
						on:change={() => {}}
					/>
				</div>

				<button
					class="bg-transparent hover:bg-gray-100 text-gray-700 dark:text-white dark:hover:bg-gray-800 rounded-full size-8 flex justify-center items-center outline-hidden focus:outline-hidden"
					on:click={onExploit}
				>
					<Exploit></Exploit></button
				>
				<!-- <div class="flex items-center text-xl font-semibold">{$i18n.t('Workspace')}</div> -->
			</div>
		</nav>
		{#if $showPreviewCode}
			<JavascriptCodeEditor lang="JavaScript" id="JavaScript" value={$currentPreviewCode}
			></JavascriptCodeEditor>
		{/if}
		{#if $showPreviewLive}
			<PhonePreview></PhonePreview>
		{/if}
		{#if $showConsoleLog}
			<JavascriptCodeEditor lang="JavaScript" id="ConsoleLog" value={$currentConsoleLog}
			></JavascriptCodeEditor>
		{/if}
	</div>
</div>
