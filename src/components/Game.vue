<template>
	<main>
		<p>{{ walls }}</p>
		<p><strong>Width:</strong> {{ width }}</p>
		<p><strong>Height:</strong> {{ height }}</p>
		<div class="cells">
			<div class="row" v-for="y of height" :key="`row-${y}`">
				<div
					class="cell-wrapper column"
					v-for="x of width"
					:key="`column-${x}`"
				>
					<div
						class="top-wall wall"
						:class="walls[x]?.[y - 1]?.top ? '' : 'other-wall'"
						@click="() => doToggle(x, y - 1, 'top')"
					></div>

					<div
						class="left-wall wall"
						:class="walls[x - 1]?.[y]?.left ? '' : 'other-wall'"
						@click="() => doToggle(x - 1, y, 'left')"
					></div>

					<div
						class="bottom-wall wall"
						:class="walls[x]?.[y]?.top ? '' : 'other-wall'"
						@click="() => doToggle(x, y, 'top')"
					></div>

					<div class="cell">{{ x }} {{ y }}</div>
				</div>

				<div
					class="right-wall wall"
					:class="walls[width]?.[y]?.left ? '' : 'other-wall'"
					@click="() => doToggle(width, y, 'left')"
				></div>
			</div>
		</div>
	</main>
</template>

<style lang="scss" scoped>
main {
	width: 90%;
	margin: 40px auto;
	max-width: 1300px;
}

.cells {
	width: 100%;
	border-top: 1px solid var(--border);
	border-right: 1px solid var(--border);
	position: relative;

	.row {
		display: flex;
	}

	.column,
	.row {
		position: relative;
	}

	.row .cell {
		border-bottom: 1px solid var(--border);
	}

	.column {
		border-left: 1px solid var(--border);
	}
}

.cell-wrapper {
	position: relative;
	width: 100%;
}
.cell {
	height: 70px;
	display: flex;
	justify-content: center;
	align-items: center;
}

.wall {
	position: absolute;
	--s: 10px;
	background: lime;

	&.other-wall {
		background: transparent;
		cursor: pointer;

		&:hover {
			background: var(--border);
		}
	}

	&.left-wall,
	&.right-wall {
		width: var(--s);
		height: 100%;
		top: 0;
		left: calc(var(--s) / 2 * -1);

		&.right-wall {
			left: initial;
			right: calc(var(--s) / 2 * -1);
		}
	}

	&.top-wall,
	&.bottom-wall {
		width: 100%;
		height: var(--s);
		top: calc(var(--s) / 2 * -1);
		left: 0;

		&.bottom-wall {
			top: initial;
			bottom: calc(var(--s) / 2 * -1);
		}
	}

	&:hover {
		--s: 20px;
		background: green;
	}
}
</style>

<script lang="ts">
// Import Vue stuff
import { ref, defineComponent } from "vue";

// Import components
import Button from "~/components/util/Button.vue";

export default defineComponent({
	components: {
		Button,
	},
	setup: () => {
		const width = ref(10);
		const height = ref(5);

		const walls = ref(
			Array(width.value + 1)
				.fill(0)
				.map((_) =>
					Array(height.value + 1)
						.fill(0)
						.map((v) => ({ left: 0, top: 0 }))
				)
		);

		function doToggle(x: number, y: number, dir: "left" | "top") {
			console.log(x, y, dir);
			if (walls.value[x][y][dir] === 0) {
				walls.value[x][y][dir] = 1;
				console.log(1);
			} else if (walls.value[x][y][dir]) {
				walls.value[x][y][dir] = 0;
				console.log(2);
			}
		}

		return {
			width,
			height,
			walls,
			doToggle,
		};
	},
});
</script>
