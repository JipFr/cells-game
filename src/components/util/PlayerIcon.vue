<template>
	<div :style="`--c: ${colors[id - 1]}`">
		<circle-icon v-if="id === 1" />
		<square-icon v-else-if="id === 2" />
		<triangle-icon v-else-if="id === 3" />
		<x-icon v-else-if="id === 4" />
	</div>
</template>

<style scoped>
svg {
	display: block;
}
.do-color {
	color: var(--c);
}
</style>

<script lang="ts">
import { defineComponent } from "vue";

// Import icons
import CircleIcon from "@icons/circle.svg";
import TriangleIcon from "@icons/triangle.svg";
import SquareIcon from "@icons/square.svg";
import xIcon from "@icons/x.svg";

export default defineComponent({
	components: {
		CircleIcon,
		TriangleIcon,
		SquareIcon,
		xIcon,
	},
	props: {
		id: {
			type: Number,
			required: true,
		},
		colors: {
			type: Array,
			required: true,
		},
	},
});
</script>
