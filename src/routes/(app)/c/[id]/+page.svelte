<script lang="ts">
	import { page } from '$app/stores';

	import Chat from '$lib/components/chat/Chat.svelte';
	import PhonePreview from '$lib/components/chat/PhonePreview.svelte';
</script>

<Chat chatIdProp={$page.params.id} />
<PhonePreview />