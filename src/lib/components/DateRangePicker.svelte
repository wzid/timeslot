<script lang="ts">
  import { createDateRangePicker, melt } from '@melt-ui/svelte';
  import { cn } from '$lib/utils';
  import CalendarIcon from '@lucide/svelte/icons/calendar';
  import ChevronLeftIcon from '@lucide/svelte/icons/chevron-left';
  import ChevronRightIcon from '@lucide/svelte/icons/chevron-right';
  import { fade } from 'svelte/transition';

  interface Props {
    title?: string | undefined;
    titlePosition?: 'top' | 'left';
    className?: string | undefined;
  }

  let { title = undefined, titlePosition = 'top', className = undefined }: Props = $props();

  const {
    elements: {
      calendar,
      cell,
      content,
      field,
      grid,
      heading,
      nextButton,
      prevButton,
      startSegment,
      endSegment,
      trigger
    },
    states: { months, headingValue, weekdays, segmentContents, open },
    helpers: { isDateDisabled, isDateUnavailable }
  } = createDateRangePicker({
    forceVisible: true,
    fixedWeeks: true
  });
</script>

<div class={cn(className, titlePosition === 'left' ? 'flex w-full items-center' : '')}>
  {#if title}
    <div class="min-w-52 select-none font-medium leading-none text-white">
      <p>{title}</p>
      <p class="text-sm font-normal text-zinc-500">What dates might work?</p>
    </div>
  {/if}
  <div
    use:melt={$field}
    class="mt-1.5 flex w-full min-w-[200px] items-center rounded-lg border border-peach-300/60 bg-zinc-800/80 p-1.5 text-peach-300"
  >
    {#each $segmentContents.start as seg}
      <div use:melt={$startSegment(seg.part)}>
        {seg.value}
      </div>
    {/each}
    <div aria-hidden="true">-</div>
    {#each $segmentContents.end as seg}
      <div use:melt={$endSegment(seg.part)}>
        {seg.value}
      </div>
    {/each}
    <div class="ml-4 flex w-full items-center justify-end">
      <button
        use:melt={$trigger}
        class="rounded-md bg-peach-300 p-1 text-zinc-900 transition-all hover:bg-peach-300/80"
      >
        <CalendarIcon size={16} />
      </button>
    </div>
  </div>
</div>
{#if $open}
  <div
    transition:fade={{ duration: 100 }}
    use:melt={$content}
    class="z-10 min-w-[320px] rounded-lg bg-zinc-900 shadow-sm"
  >
    <div use:melt={$calendar} class="w-full rounded-lg bg-zinc-800/90 p-3 text-white shadow-sm">
      <header class="flex items-center justify-between pb-2">
        <button use:melt={$prevButton} class="pagnation-button">
          <ChevronLeftIcon size={24} />
        </button>
        <div use:melt={$heading} class="font-semibold">
          {$headingValue}
        </div>
        <button use:melt={$nextButton} class="pagnation-button">
          <ChevronRightIcon size={24} />
        </button>
      </header>
      <div class="flex items-center gap-6">
        {#each $months as month}
          <table use:melt={$grid} class="w-full">
            <thead aria-hidden="true">
              <tr>
                {#each $weekdays as day}
                  <th>
                    <div>
                      {day}
                    </div>
                  </th>
                {/each}
              </tr>
            </thead>
            <tbody>
              {#each month.weeks as weekDates}
                <tr>
                  {#each weekDates as date}
                    <td
                      role="gridcell"
                      aria-disabled={$isDateDisabled(date) || $isDateUnavailable(date)}
                    >
                      <div use:melt={$cell(date, month.value)}>
                        {date.day}
                      </div>
                    </td>
                  {/each}
                </tr>
              {/each}
            </tbody>
          </table>
        {/each}
      </div>
    </div>
  </div>
{/if}

<style lang="postcss">
  [data-melt-datefield-label][data-invalid] {
    @apply text-red-500;
  }

  [data-melt-datefield-field][data-invalid] {
    @apply border-red-400;
  }

  [data-melt-datefield-segment][data-invalid] {
    @apply text-red-500;
  }

  [data-melt-datefield-segment]:not([data-segment='literal']) {
    @apply px-0.5;
  }

  [data-melt-datefield-validation] {
    @apply self-start text-red-500;
  }

  .pagnation-button {
    @apply rounded-lg p-1 transition-all hover:bg-peach-400/20 disabled:pointer-events-none disabled:rounded-lg disabled:p-1 disabled:opacity-40;
  }

  th {
    @apply text-sm font-semibold;

    & div {
      @apply flex h-6 w-6 items-center justify-center p-4;
    }
  }

  [data-melt-calendar-cell] {
    @apply flex h-6 w-6 cursor-pointer select-none items-center justify-center rounded-lg p-4 hover:bg-peach-400/20 focus:ring focus:ring-peach-400;
  }

  [data-melt-calendar-cell][data-highlighted] {
    @apply bg-peach-400/20;
  }

  [data-melt-calendar-cell][data-range-highlighted] {
    @apply bg-peach-400/20;
  }

  [data-melt-calendar-cell][data-disabled] {
    @apply pointer-events-none opacity-40;
  }

  [data-melt-calendar-cell][data-unavailable] {
    @apply pointer-events-none text-red-400 line-through;
  }

  [data-melt-calendar-cell][data-selected] {
    @apply bg-peach-300 text-zinc-900;
  }

  [data-melt-calendar-cell][data-outside-visible-months] {
    @apply pointer-events-none cursor-default opacity-40 hover:bg-transparent;
  }

  [data-melt-calendar-cell][data-outside-month] {
    @apply pointer-events-none cursor-default opacity-0 hover:bg-transparent;
  }
</style>
