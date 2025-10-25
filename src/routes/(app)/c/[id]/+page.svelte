<script lang="ts">
	import { page } from '$app/stores';

	import Chat from '$lib/components/chat/Chat.svelte';
	import PhonePreview from '$lib/components/chat/PhonePreview.svelte';
	import JavascriptCodeEditor from '$lib/components/common/JavascriptCodeEditor.svelte';
</script>

<Chat chatIdProp={$page.params.id} />
