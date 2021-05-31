<template>
	<main>
		<p>{{ colors }}</p>
		<p>{{ turn }}</p>
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
						:class="e(x, y - 1)?.top ? '' : 'other-wall'"
						:style="e(x, y - 1).top && `--c: ${colors[e(x, y - 1).top - 1]}`"
						@click="() => doToggle(x, y - 1, 'top')"
					></div>

					<div
						class="left-wall wall"
						:class="e(x - 1, y)?.left ? '' : 'other-wall'"
						:style="e(x - 1, y).left && `--c: ${colors[e(x - 1, y).left - 1]}`"
						@click="() => doToggle(x - 1, y, 'left')"
					></div>

					<div
						class="bottom-wall wall"
						:class="walls[x]?.[y]?.top ? '' : 'other-wall'"
						:style="e(x, y).top && `--c: ${colors[e(x, y).top - 1]}`"
						@click="() => doToggle(x, y, 'top')"
					></div>

					<div class="cell">{{ x }} {{ y }}</div>
				</div>

				<div
					class="right-wall wall"
					:class="e(width, y)?.left ? '' : 'other-wall'"
					:style="e(width, y).left && `--c: ${colors[e(width, y).left - 1]}`"
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
	background: var(--c, linear-gradient(to right, green, orange));

	&.other-wall {
		background: transparent;

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
		cursor: pointer;
	}
}
</style>

<script lang="ts">
// Import Vue stuff
import { ref, defineComponent } from "vue";

// Import components
import Button from "~/components/util/Button.vue";

// Types
type Direction = "left" | "top";

export default defineComponent({
	components: {
		Button,
	},
	setup: () => {
		const width = ref(5);
		const height = ref(5);
		const colors = ref([
			"rgb(255, 222, 91)",
			"rgb(175, 82, 222)",
			"rgb(255, 45, 85)",
			"rgb(52, 199, 89)",
		]);

		const playerCount = ref(2);
		const turn = ref(1);

		const walls = ref(
			Array(width.value + 1)
				.fill(0)
				.map((_) =>
					Array(height.value + 1)
						.fill(0)
						.map((v) => ({ left: 0, top: 0 }))
				)
		);

		function doToggle(x: number, y: number, dir: Direction) {
			// console.log(x, y, dir);
			if (walls.value[x][y][dir] == 0) {
				walls.value[x][y][dir] = turn.value;
				console.log(turn.value);
				turn.value++;
				turn.value = ((turn.value - 1) % playerCount.value) + 1;
				console.log(turn.value, "-");
			}
		}

		function e(x: number, y: number) {
			return walls.value[x]?.[y];
		}

		return {
			width,
			height,
			walls,
			doToggle,
			colors,
			e,
			turn,
		};
	},
});
</script>
