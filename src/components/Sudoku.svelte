<script>
    import _ from 'lodash';
    import { fade } from 'svelte/transition';
    import Cell from './Cell.svelte';
    import { message } from '../store/sudoku';
    import { getFocusCellsRow, getFocusCellsCol, getFocusCellsBox } from '../utils/sudoku';

    export let board = [];
    export let answer = [];
    export let memo = [];
    export let selectedPoint = {x:0, y:0};
    $: selectedAnswer = answer?.[selectedPoint.y]?.[selectedPoint.x];
    $: focusCellsRow = getFocusCellsRow(selectedPoint);
    $: focusCellsCol = getFocusCellsCol(selectedPoint);
    $: focusCellsBox = getFocusCellsBox(selectedPoint);
    $: focusCells = _.uniqWith([...focusCellsRow, ...focusCellsCol, ...focusCellsBox], _.isEqual);

    function isError (selectedPoint) {
        const value = answer[selectedPoint.y][selectedPoint.x];
        if (!value) return false;

        const row = getFocusCellsRow(selectedPoint);
        const col = getFocusCellsCol(selectedPoint);
        const square = getFocusCellsBox(selectedPoint);
        const rowValues = row.map(p => answer[p.y][p.x]).filter(x => !!x);
        const colValues = col.map(p => answer[p.y][p.x]).filter(x => !!x);
        const squareValues = square.map(p => answer[p.y][p.x]).filter(x => !!x);
        return rowValues.filter(x => x === value).length > 1 ||
            colValues.filter(x => x === value).length > 1 ||
            squareValues.filter(x => x === value).length > 1
    }
</script>

<div class="wrapper">
    <table class="game-table">
        {#if $message}
            <div out:fade class="message">{$message}</div>
        {/if}
        <tbody>
            {#each answer as row, y (y)}
                <tr>
                    {#each row as item, x(x)}
                        <td
                            class:is-answer={board[y][x] === 0}
                            class:selected={selectedPoint.x === x && selectedPoint.y === y}
                            class:active={focusCells.find(p => p.x === x && p.y === y)}
                            class:highlight={selectedAnswer && item === selectedAnswer}
                            class:error={isError({x,y})}
                        >
                            <Cell
                                value={item}
                                memo={memo[y][x]}
                                on:click={() => selectedPoint = {x,y}}
                            />
                        </td>
                    {/each}
                </tr>
            {/each}
        </tbody>
    </table>
</div>

<style>
    .wrapper {
        width: 600px;
        height: 600px;
        position: relative;
    }
    .game-table {
        display: block;
        height: 100%;
        width: 100%;
        box-sizing: border-box;
        border: 2px solid #344861;
    }
    .game-table:after {
        content: '';
        border-left: 2px solid #344861;
        border-right: 2px solid #344861;
        box-sizing: border-box;
        left: 33.3333%;
        width: 33.3333%;
        position: absolute;
        top: 0;
        height: 100%;
        pointer-events: none;
    }
    .game-table tbody:after {
        content: '';
        border-top: 2px solid #344861;
        border-bottom: 2px solid #344861;
        box-sizing: border-box;
        pointer-events: none;
        width: 100%;
        height: 33.3333%;
        position: absolute;
        top: 33.3333%;
        left: 0;
    }
    .game-table tbody {
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;
    }
    .game-table tr {
        height: 11.1111%;
        display: flex;
    }
    .game-table td {
        width: 100%;
        border-right: 1px solid #bec6d4;
        border-bottom: 1px solid #bec6d4;
        box-sizing: border-box;
        cursor: pointer;
    }
    .game-table td.active {
        background-color: #e2e7ed;
    }
    .game-table td.highlight {
        background-color: #f7cfd6;
    }
    .game-table td.error {
        background-color: #f7cfd6;
    }
    .game-table td.is-answer :global(button) {
        fill: #fb3d3f;
    }
    .game-table td.selected {
        background-color: #bbdefb;
    }
    .message {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        z-index: 1;
        text-align: center;
        background-color: #fff;
        opacity: 0.8;
        font-size: 25px;
    }
    .message:after {
        content: '';
        display: inline-block;
        height: 100%;
        vertical-align: middle;
    }
</style>