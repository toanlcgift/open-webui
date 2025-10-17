<script>
	let imageSrc = '';

	const socket = new WebSocket('ws://localhost:5000/stream');
	socket.onmessage = (event) => {
		imageSrc = `data:image/png;base64,${event.data}`;
	};
</script>

<div class="w-230 rounded-3xl p-4 flex items-center justify-center shadow-md">
	<img src={imageSrc} alt="Live Stream" class="rounded-xl shadow-lg" />
</div>
