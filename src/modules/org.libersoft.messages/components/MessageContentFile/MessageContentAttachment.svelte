<script lang="ts">
	import AttachmentContainer from './FileContainer.svelte';
	let { node /* level, num_siblings */ } = $props();
	let uploadId = $state(node.attributes.id?.value);
</script>

<AttachmentContainer {uploadId} />
