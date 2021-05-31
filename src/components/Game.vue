<template>
	<main class="do-layout">
		<div class="main-content">
			<div class="info">
				<p><strong>Width:</strong> {{ width }}</p>
				<p><strong>Height:</strong> {{ height }}</p>
			</div>
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
							:style="
								e(x, y - 1).top && `--c: ${colors[e(x, y - 1).top.player - 1]}`
							"
							@click="() => doToggle(x, y - 1, 'top')"
						></div>

						<div
							class="left-wall wall"
							:class="e(x - 1, y)?.left ? '' : 'other-wall'"
							:style="
								e(x - 1, y).left &&
								`--c: ${colors[e(x - 1, y).left.player - 1]}`
							"
							@click="() => doToggle(x - 1, y, 'left')"
						></div>

						<div
							class="bottom-wall wall"
							:class="walls[x]?.[y]?.top ? '' : 'other-wall'"
							:style="e(x, y).top && `--c: ${colors[e(x, y).top.player - 1]}`"
							@click="() => doToggle(x, y, 'top')"
						></div>

						<div class="cell">{{ x }} {{ y }}</div>
					</div>

					<div
						class="right-wall wall"
						:class="e(width, y)?.left ? '' : 'other-wall'"
						:style="
							e(width, y).left && `--c: ${colors[e(width, y).left.player - 1]}`
						"
						@click="() => doToggle(width, y, 'left')"
					></div>
				</div>
			</div>
		</div>
		<aside>
			<div class="players">
				<div
					class="color-btn player-display"
					v-for="i of playerCount"
					:key="`player-${i}`"
					:style="`--c: ${colors[(i - 1) % colors.length]}`"
					:class="turn === i ? 'is-turn' : ''"
				>
					<circle-icon v-if="i === 1" />
					<square-icon v-else-if="i === 2" />
					<triangle-icon v-else-if="i === 3" />
					<x-icon v-else-if="i === 4" />
				</div>
			</div>
		</aside>
	</main>
</template>

<style lang="scss" scoped>
main {
	width: 90%;
	margin: 40px auto;
	max-width: 1300px;
}
.do-layout {
	display: grid;
	grid-template-columns: 1fr 250px;
	grid-gap: 20px;
}

.info {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
	grid-gap: 10px;
}

.cells {
	width: 100%;
	--border-size: 1px;
	border: var(--border-size) solid var(--border);
	position: relative;
	border-radius: 10px;
	overflow: hidden;

	.row {
		display: flex;
	}

	.column,
	.row {
		position: relative;
	}

	.row:not(:last-child) {
		border-bottom: var(--border-size) solid var(--border);
	}

	.column + .column {
		border-left: var(--border-size) solid var(--border);
	}
}

.cell-wrapper {
	position: relative;
	width: 100%;
}
.cell {
	height: 100px;
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

.players {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
	grid-gap: 10px;

	.player-display {
		// background: var(--c);
		display: flex;
		justify-content: center;
		padding: 20px;
		border-radius: 9px;
		position: relative;
		border: 3px solid var(--c);

		svg {
			display: block;
		}

		&.is-turn {
			background: var(--c);
			color: white;
		}
	}
}

@media (max-width: 900px) {
	.do-layout {
		grid-template-columns: 100%;
	}
	.players {
		background: var(--body);
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		padding: 5px 50px;
		box-sizing: border-box;
		grid-template-columns: repeat(auto-fit, minmax(1px, 1fr));
		border-bottom: 1px solid var(--border);

		.player-display {
			padding: 10px;
		}
	}
}
</style>

<script lang="ts">
// Import Vue stuff
import { ref, defineComponent } from "vue";

// Import components
import Button from "~/components/util/Button.vue";

// Import icons
import CircleIcon from "@icons/circle.svg";
import TriangleIcon from "@icons/triangle.svg";
import SquareIcon from "@icons/square.svg";
import xIcon from "@icons/x.svg";

// Types
type Direction = "left" | "top";

interface Cell {
	player: number;
	placedAt: number;
}

export default defineComponent({
	components: {
		Button,
		CircleIcon,
		TriangleIcon,
		SquareIcon,
		xIcon,
	},
	setup: () => {
		const width = ref(10);
		const height = ref(5);
		const colors = ref([
			"rgb(255, 222, 91)",
			"rgb(175, 82, 222)",
			"rgb(255, 45, 85)",
			"rgb(52, 199, 89)",
		]);

		const playerCount = ref(2);
		const turn = ref(1);

		const walls = ref<{ left: Cell | null; top: Cell | null }[][]>(
			Array(width.value + 1)
				.fill(false)
				.map((_) =>
					Array(height.value + 1)
						.fill(false)
						.map((_) => ({ left: null, top: null }))
				)
		);

		function doToggle(x: number, y: number, dir: Direction) {
			// console.log(x, y, dir);
			if (walls.value[x][y][dir] == null) {
				walls.value[x][y][dir] = {
					player: turn.value,
					placedAt: Date.now(),
				};
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
			playerCount,
		};
	},
});
</script>
