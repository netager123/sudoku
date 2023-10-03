<script>
    import { createEventDispatcher } from "svelte";
    import { memoFlag, remainHint} from '../store/sudoku';

    const dispatch = createEventDispatcher();
</script>

<div class="wrapper">
    <div
        class="item"
        on:click={() => dispatch('remove')}
    >
        <div class="text">지우기</div>
    </div>

    <div
        class="item memo"
        class:active={$memoFlag}
        on:click={() => $memoFlag = !$memoFlag}
    >
        <div class="sub-text">{$memoFlag ? 'ON' : 'OFF'}</div>
        <div class="text">메모</div>
    </div>
    
    <div
        class="item hint"
        on:click={() => dispatch('hint')}
    >
        <div class="sub-text">{$remainHint}</div>
        <div class="text">힌트</div>
    </div>

    <div
        class="item new-game"
        on:click={() => dispatch('newGame')}
    >
        <div class="text">새로운 게임</div>
    </div>
</div>

<style>
    .wrapper {
        display: flex;
        margin-top: 10px;
    }
    .item {
        width: 100%;
        text-align: center;
        cursor: pointer;
        position: relative;
    }
    .icon {
        width: 35px;
        height: 35px;
    }
    .new-game {
        width: 100%;
        height: 100%;
        background-color: #8f7a66;
        border-radius: 3px;
        color: #fff;
        display: inline-block;
    }
    .new-game > .text:after {
        content: '';
        display: inline-block;
        height: 100%;
        vertical-align: middle;
    }
    .sub-text {
        font-size: 13px;
        position: absolute;
        top: 0;
        left: 55%;
        border-radius: 10px;
    }
    .memo > .sub-text {
        background-color: #94a3b7;
        color: #fff;
        padding: 5px;
    }
    .memo.active > .sub-text {
        background-color: #4a90e2;
    }
    .hint > .sub-text {
        background-color: #fb3d3f;
        color: #fff;
        padding: 2px 5px;
    }
</style>