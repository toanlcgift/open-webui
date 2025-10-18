<script>
	let imageSrc = '';

	const socket = new WebSocket('ws://localhost:5117/ws');
	socket.onmessage = (event) => {
		console.log('Received data ' + event.data);
		imageSrc = `data:image/png;base64,${event.data}`;
		socket.send('ping');
	};
	socket.onopen = (event) => {
		console.log('WebSocket connection established');
		socket.send('ping');
	};
</script>

<div class="w-230 rounded-3xl p-4 flex items-center justify-center shadow-md">
	<img src={imageSrc} alt="Live Stream" class="rounded-xl shadow-lg" />
</div>
