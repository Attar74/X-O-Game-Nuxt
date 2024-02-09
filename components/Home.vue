<template>
  <div class="flex my-auto h-[90vh] p-[2rem] lg:py-[0rem]">
		<div class="mx-auto my-auto">
			<div class="text-7xl mb-[2rem] text-center"> 
				<span class="border-2 px-2">X</span>
				<span class="border-2 px-2">O</span> 
				<span class="tracking-widest"> Game </span>
			</div>
			<p class="text-5xl mb-[2rem] text-center text-red-500" v-if="isEqual">
				No Winner, Hit Reset Button
			</p>
			<p class="text-5xl mb-[2rem] text-center" v-else-if="!GameDone"> Player 
				<span class="text-7xl" :class="[isX ? 'text-[#09C372]' : 'text-[#498AFB]']">
					{{ isX ? 'X' : 'O' }}
				</span>
				's turn
			</p>
			<p class="text-5xl mb-[2rem] text-center" v-else>
				Whoaaaaa!! Player
				<span class="text-7xl" :class="[!isX ? 'text-[#09C372]' : 'text-[#498AFB]']">
					{{ !isX ? 'X' : 'O' }}
				</span>
				Won 
			</p>
			<div class="flex justify-center" v-for="j in 3" :key="j">
				<button 
					:disabled="GameDone" 
					class="flex border-4 m-[0.1rem] border-[#000] text-[#000] w-[5.375rem] h-[5.375rem] md:w-[10.375rem] md:h-[10.375rem] rounded-md"
					:class="{'hover:bg-[#00000033] cursor-pointer': !GameDone}"
					v-for="i in 3" @click="doIt(j, i)" :key="i">
					<p class="text-[3rem] md:text-[5rem] mx-auto my-auto" :class="[game[j-1][i-1] == 'x' ? 'text-[#09C372]' : 'text-[#498AFB]']">
						{{ game[j-1][i-1] }}
					</p>
				</button>
			</div>
			<div class="flex">
				<button @click="reset" class="bg-[#FF3860] text-[#fff] px-10 py-2 rounded-md mx-auto mt-[2rem] hover:bg-[#ff3860d9]">
					Reset
				</button>
			</div>
		</div>
	</div>
</template>
<script lang="ts" setup>

let isX = ref(true)
let numberOfClicks = ref(0)
let isEqual = ref(false)
let GameDone = ref(false)
const game: string[][] = reactive([
	['', '', ''],
	['', '', ''],
	['', '', ''],
])

watch(numberOfClicks, (val) => {
  if(val >= 9 && !GameDone.value) {
	isEqual.value = true
  }
})


const doIt = (j: number, i: number) => {
	if(game[j-1][i-1].length) {
		return
	}
	numberOfClicks.value++;
	game[j-1][i-1] = game[j-1][i-1].length ? game[j-1][i-1] : isX.value ? 'x' : 'o'
	isX.value = !isX.value
	whoWin(j, i)
}


const whoWin = (j: number, i:number) => {
	checkRows()
	checkCols(j, i)
	checkAnalog()
}

const checkRows = () => {
	game.map((row) => {
		if(row.every( val => val === 'x') || row.every( val => val === 'o')) {
			
			GameDone.value = true
		}
	})
}

const checkCols = (j: number, i: number ) => {
	if(GameDone.value)
		return
	const arrCol:string[] = []
	game.map((row) => {
		arrCol.push(row[i-1])
	})
	GameDone.value = checkArr(arrCol)
}

const checkArr = (arr: string[]) => {
	if(GameDone.value) {
		return true
	}
	const XOrO = !isX.value ? 'x' : 'o'
	return arr.every(val => val === XOrO)
}

const checkAnalog = () => {
	if(GameDone.value)
		return

	const arrLeftAnalog:string[] = []
	const arrRightAnalog:string[] = []
	game.map((row, i) => {
		row.map((item, j) => {
			if( i === j) {
				arrLeftAnalog.push(game[i][j])
			} 
			else if (i + j === 2) {
				arrRightAnalog.push(game[i][j])
			}
		})
	})
	GameDone.value = checkArr(arrLeftAnalog)
	GameDone.value = checkArr(arrRightAnalog)
}

const reset = () => {
	game.map((row, j) => {
		row.map((item, i) => {
			game[j][i] = ''
		})
	})
	GameDone.value = false
	isX.value = isEqual.value = true
}
</script>
<style>
@import url('../assets/css/styles.css');
</style>