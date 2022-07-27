<template>
	<div id="app">
		<h1 style="color: gray">minSweeping</h1>

		<div style="padding: 10px">
			<div v-for="(row, y) in dataA" class="box">
				<button
					v-for="(item, x) in row"
					:class="{ ismine: item.mine, btn: true }"
					:style="numberColor[item.adjacentMines]"
					@click="onClick(item)"
				>
					<div v-if="item.revealed || dev">
						<div v-if="item.mine">
							<img src="..\public\1.svg" />
						</div>

						<div v-else>{{ item.adjacentMines }}</div>
					</div>
				</button>
			</div>
		</div>
		<button @click="resit">重置</button>
		<div @click="themSwitch" style="higth: 50px; width: 50px">
			<img
				src="..\public\2.svg"
				v-if="islight"
				style="higth: 50px; width: 50px"
			/>

			<img
				v-else
				src="..\public\3.svg"
				style="higth: 50px; width: 50px"
			/>
		</div>
	</div>
</template>

<script>
const size = 10;
function BlockState(x, y) {
	this.x = x;
	this.y = y;
	this.revealed = false;
	this.mine = false;
	this.flagged = false;
	this.adjacentMines = 0;
	this.site = y * 10 + x + 1;
}

function generateMines(data) {
	data.forEach((row, y) => {
		row.forEach((item, x) => {
			item.mine = Math.random() < 0.2;
		});
	});
}

function updatedNumbers(data) {
	data.forEach((row, y) => {
		row.forEach((item, x) => {
			if (item.mine) return;

			adjacents.forEach(([dx, dy]) => {
				let x2 = dx + x;
				let y2 = dy + y;

				if (x2 >= size || x2 < 0 || y2 >= size || y2 < 0) return;
				// console.log(x2, y2);

				if (data[y2][x2].mine) {
					item.adjacentMines += 1;
				}
			});
		});
	});
}

let adjacents = [
	[1, 1],
	[1, 0],
	[0, 1],
	[-1, -1],
	[-1, 0],
	[0, -1],
	[1, -1],
	[-1, 1],
];

export default {
	data() {
		return {
			islight: true,
			dev: false,
			numberColor: [
				'color:#8bc34a',
				'color:skyblue',
				'color:#00bcd4',
				'color:orange',
				'color:pink',
				'color:purple',
				'color:#cddc39',
				'color:#ff5722',
				'color:red',
			],
			dataA: Array(size)
				.fill()
				.map((_, y) =>
					Array(size)
						.fill()
						.map((_, x) => new BlockState(x, y))
				),
		};
	},
	methods: {
		onClick(item) {
			if (item.adjacentMines === 0 && !item.mine) {
				adjacents.forEach(([dx, dy]) => {
					let x2 = dx + item.x;
					let y2 = dy + item.y;

					if (
						x2 >= size ||
						x2 < 0 ||
						y2 >= size ||
						y2 < 0 ||
						item.adjacents ||
						item.mine
					)
						return;
					this.dataA[y2][x2].revealed = true;
				});
			}

			item.revealed = true;
			if (item.mine) {
				alert('BOM');
			}
		},
		resit() {
			location.reload();
		},

		themSwitch() {
			this.islight = !this.islight;
			if (this.islight) {
				document.documentElement.style.background = 'white';
				localStorage.theme = 'light';
			} else {
				document.documentElement.style.background = 'black';
				localStorage.theme = 'dark';
			}
		},
	},
	computed: {},

	mounted() {
		const data = this.dataA;

		document.documentElement.style.transition = 'all 1s ease';
		if (localStorage.theme === 'light') {
			this.islight = true;
			document.documentElement.style.background = 'white';
		} else {
			this.islight = false;
			document.documentElement.style.background = 'black';
		}

		let generate = false;
		generateMines(data, generate);
		updatedNumbers(data);
	},
};
</script>

<style lang="less">
@size: 35px;

.btn {
	width: @size;
	height: @size;
	color: rgb(7, 6, 6);
	border: 1px solid rgba(0, 0, 0, 0.6);
	background-color: rgba(255, 255, 255, 0.1);
	&:hover {
		background-color: rgba(255, 255, 255, 0.8);
	}
}

.ismine {
	color: rgb(210, 159, 159);
	// border: rgba(220, 49, 49, 0.8) 0.5px solid;
}
.box {
	display: flex;
}

.them() {
	.bark {
		background: black;
		color: white;
		transition: all 1s;
	}
	.ligth {
		background: white;
		color: black;
		transition: all 1s;
	}
}
</style>
