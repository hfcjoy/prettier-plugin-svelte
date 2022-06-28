<script>
  import {getContext} from 'svelte';
  import {IMAGES_NEWS} from '@/const/images';
  import CheckBox from '@/widgets/CheckBox.svelte';
  import Pagination from '@/widgets/Pagination.svelte';
  import {followingTradersPageNum$, followingTradersState$} from '../../streams';
  import Trader from './TraderView.svelte';
  import lang from '../../CopyTradingListPage.lang';

  const LG = getContext('LG');

  let showRealPositon = true;

  $: currentPageNum = $followingTradersPageNum$ || 1;
  $: total = $followingTradersState$.total;
  $: loading = $followingTradersState$.loading;
  $: followingTraders = $followingTradersState$.rows;

  function handlePageChange(event) {
    followingTradersPageNum$.next(event.detail.current);
  }
</script>

<style>
  .empty-wrap {
    height: 414px;
  }
  .follow-main.loading {
    height: 500px;
  }
  .empty-img {
    width: 90px;
    height: 90px;
  }
</style>

<main class="follow-main" class:loading>
  {#if !loading && total > 0}
    <div class="df aic mt28">
      <CheckBox bind:checked={showRealPositon} />
      <div class="ml10 roboto f16 fw2 T3">{LG(lang.ShowRealWarehouse)}</div>
    </div>
  {/if}

  {#each followingTraders as trader (trader.userId)}
    <Trader {trader} {showRealPositon} />
  {/each}

  {#if !loading && total < 1}
    <div class="empty-wrap df fdc aic jcc">
      <img src={IMAGES_NEWS.NO_ATTENTION_YET} width="90" height="90" class="empty-img" alt="phemex" srcset="" />
      <div class="roboto f16 fw2 T3">{LG(lang.NoPosition)}</div>
    </div>
  {/if}
</main>

{#if total > 0}
  <div class="df fdr p24 jcfe">
    <Pagination {total} current={currentPageNum} on:nextPage={handlePageChange} />
  </div>
{/if}
