<script>
	import { onMount } from 'svelte';
	import _ from 'lodash';
	import Difficulty from './components/Difficulty.svelte';
	import Navigation from './components/Navigation.svelte';
	import NumberPad from './components/NumberPad.svelte';
	import Sudoku from './components/Sudoku.svelte';
	import { getSudoku } from './utils/sudoku.js';
	import { remainHint, message, memoFlag } from './store/sudoku';

	let difficulty = localStorage.getItem('sudoky.difficuly') || 'easy';
	let selectedPoint = {x:0, y:0};
	let { board } = [] ; //JSON.parse(localStorage.getItem('sudoku.board')) || [];
	let answer = []; //JSON.parse(localStorage.getItem('sudoku.answer')) || [];
	let memo = []; //JSON.parse(localStorage.getItem('sudoku.memo')) || [];
	let solution = []; //JSON.parse(localStorage.getItem('sudoku.solution')) || [];
	$remainHint = 0; //Number(localStorage.getItem('sudoku.remainHint')) || 0;
	$: isEditable = !(board?.[selectedPoint.y]?.[selectedPoint.x]);
	$: if (answer.length !== 0 && _.isEqual(answer, solution)) {
		$message = '훌륭합니다.'
	}
	$: localStorage.setItem('sudoku.difficulty', difficulty);
	$: localStorage.setItem('sudoku.answer', JSON.stringify(answer));
	$: localStorage.setItem('sudoku.remainHint',  $remainHint);
	$: localStorage.setItem('sudoku.memo', JSON.stringify(memo));
	$: localStorage.setItem('sudoku.solution', JSON.stringify(solution));
	$: localStorage.setItem('sudoku.board', JSON.stringify(board));

	onMount(() => {
		if (solution.length) return;
		handleNewGame();
	});

	function handleRemove() {
		console.log('remove');
		if (!isEditable) return;
		memo[selectedPoint.y][selectedPoint.x] = [];
		answer[selectedPoint.y][selectedPoint.x] = 0;
	}

	function handleHint() {
		console.log('hint');
		if (!isEditable || $remainHint <= 0) return;
		$remainHint--;
		answer[selectedPoint.y][selectedPoint.x] = solution[selectedPoint.y][selectedPoint.x];
	}

	function handleNewGame() {
		console.log('new game');
		$message = '새로운 게임을 생성 중입니다.';
		setTimeout(() => {
			const result = getSudoku(difficulty);
			solution = result.solution;
			board = result.board;
			answer = _.cloneDeep(result.board);

			const tmpMemo = [];
			for (let i=0; i<9; i++) {
				for (let j=0; j<9; j++) {
					if (!tmpMemo[i]) tmpMemo[i] = [];
					if (!tmpMemo[i][j]) tmpMemo[i][j] = [];
				}
			}
			memo = tmpMemo;
			$message = '';
			$remainHint = 3;
		});
	}

	function handleClickNumber ({detail}) {
		console.log(detail);
		if (!isEditable) return;
		if ($memoFlag) {
			const memoList = memo[selectedPoint.y][selectedPoint.x];
			const index = memoList.indexOf(detail);
			if (index >= 0) {
				memoList.splice(index, 1);
			} else {
				memoList.push(detail);
			}
			memo[selectedPoint.y][selectedPoint.x] = memoList;
		} else {
			answer[selectedPoint.y][selectedPoint.x] = detail;
		}
	}
</script>

<div class="contents">
	<Difficulty bind:difficulty />

	<Sudoku
		bind:selectedPoint
		{answer}
		{memo}
		{board}
	/>

	<Navigation
		on:remove={handleRemove}
		on:hint={handleHint}
		on:newGame={handleNewGame}
	/>
	<NumberPad
		on:click={handleClickNumber}
	/>
</div>



<style>
	.contents {
		width: 600px;
		margin: auto;
	}
</style>