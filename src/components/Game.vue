<template>
	<main class="do-layout">
		<div class="main-content">
			<div class="info">
				<p><strong>Width:</strong> {{ width }}</p>
				<p><strong>Height:</strong> {{ height }}</p>
			</div>
			<div class="info">
				<div
					class="player-points"
					v-for="player of Object.keys(Array(playerCount).fill(0))"
					:key="`score-${player}`"
				>
					Player
					<player-icon
						class="do-color"
						:id="Number(player) + 1"
						:colors="colors"
					/>:
					{{ points[Number(player) + 1] || 0 }}
				</div>
			</div>
			<div class="cells">
				<div class="row" v-for="y of height" :key="`row-${y}`">
					<div
						class="cell-wrapper column"
						v-for="x of width"
						:key="`column-${x}`"
						:style="
							filledSquares[`${x}-${y}`] &&
							`--cell-bg: ${colors[filledSquares[`${x}-${y}`] - 1]}`
						"
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

						<div class="cell">
							<player-icon
								v-if="filledSquares[`${x}-${y}`]"
								class="do-color"
								:id="filledSquares[`${x}-${y}`]"
								:colors="colors"
							/>
						</div>
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
					<player-icon :id="i" :colors="colors" />
				</div>
			</div>
		</aside>
	</main>
</template>

<style lang="scss">
.player-points {
	display: flex;
	align-items: center;
	margin: 10px 0;

	svg {
		margin: 0;
		margin-left: 10px;
	}
}
</style>

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
	// background: var(--cell-bg, transparent);
}

.wall {
	position: absolute;
	--s: 10px;
	background: var(--c, linear-gradient(to right, green, orange));
	box-shadow: 0 0 10px var(--c);

	&.other-wall {
		background: transparent;

		&:hover {
			background: var(--border);
		}
	}

	&.wall {
		z-index: 10;
	}

	&.left-wall,
	&.right-wall {
		width: var(--s);
		height: calc(100% + 10px);
		top: -5px;
		left: calc(var(--s) / 2 * -1);

		&.right-wall {
			left: initial;
			right: calc(var(--s) / 2 * -1);
		}
	}

	&.top-wall,
	&.bottom-wall {
		width: calc(100% + 10px);
		height: var(--s);
		top: calc(var(--s) / 2 * -1);
		left: -5px;

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

		&.is-turn {
			background: var(--c);
			color: white;
		}

		&.is-turn .player-1 {
			color: black;
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
import { ref, defineComponent, computed } from "vue";

// Import components
import PlayerIcon from "~/components/util/PlayerIcon.vue";

// Types
type Direction = "left" | "top";

interface Cell {
	player: number;
	placedAt: number;
}
interface FilledSquares {
	[key: string]: number;
}

export default defineComponent({
	components: {
		PlayerIcon,
	},
	setup: () => {
		const width = ref(10);
		const height = ref(7);
		const colors = ref([
			"rgb(255, 243, 195)",
			"rgb(22, 45, 250)",
			"#EA60F0",
			"rgb(52, 199, 89)",
		]);

		const playerCount = ref(colors.value.length);
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
				turn.value++;
				turn.value = ((turn.value - 1) % playerCount.value) + 1;
			}
		}

		function e(x: number, y: number) {
			return walls.value[x]?.[y];
		}
		let points = ref<FilledSquares>({});

		const filledSquares = computed(() => {
			let prevPoints = Object.assign({}, points.value);
			console.log(walls.value);
			console.clear();
			points.value = {};
			console.log(prevPoints);

			let filledSquares: FilledSquares = {};
			for (let x = 0; x < walls.value.length; x++) {
				for (let y = 0; y < walls.value[x].length; y++) {
					let wallList = [
						walls.value[x]?.[y]?.left,
						walls.value[x - 1]?.[y]?.left,
						walls.value[x]?.[y - 1]?.top,
						walls.value[x]?.[y]?.top,
					]
						.filter((v) => v)
						.sort((a, b) => (b?.placedAt || 0) - (a?.placedAt || 0));

					if (wallList.length === 4) {
						const playerId = wallList[0]?.player || 0;
						filledSquares[`${x}-${y}`] = playerId;

						if (!points.value[playerId]) points.value[playerId] = 0;
						points.value[playerId]++;
					}
				}
			}

			for (let key of Object.keys(points.value)) {
				if (prevPoints[key] !== points.value[key]) {
					turn.value = Number(key);
				}
			}

			return filledSquares;
		});

		return {
			width,
			height,
			walls,
			doToggle,
			colors,
			e,
			turn,
			playerCount,
			filledSquares,
			points,
		};
	},
});
</script>
