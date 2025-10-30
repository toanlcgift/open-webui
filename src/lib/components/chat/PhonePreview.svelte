<script>
	import { OPENAI_API_V1_BASE_URL } from '$lib/constants';
	let imageSrc = '';

	const socket = new WebSocket(OPENAI_API_V1_BASE_URL.replace('http', 'ws') + '/ws');
	socket.onmessage = (event) => {
		if (event.data && event.data !== '') {
			imageSrc = `data:image/png;base64,${event.data}`;
		}
		socket.send('ping');
	};
	socket.onopen = (event) => {
		console.log('WebSocket connection established');
		socket.send('ping');
	};
</script>

<div class="p-4 flex items-center justify-center">
	{#if imageSrc !== ''}
		<img src={imageSrc} alt="Live Stream" class="rounded-xl shadow-lg max-h-[86vh]" />
	{/if}
</div>
