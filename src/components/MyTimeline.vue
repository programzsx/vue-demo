<template>
  <div ref="timelineContainer" class="timeline-container"></div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import * as d3 from 'd3';

const timeFormat = d3.timeFormat;

const timelineContainer = ref<HTMLDivElement | null>(null);

onMounted(() => {
  if (timelineContainer.value) {
    const data = [
      { date: new Date(2025, 1, 5), event: '事件1' },
      { date: new Date(2025, 3, 15), event: '事件2' },
      { date: new Date(2025, 6, 20), event: '事件3' },
      { date: new Date(2025, 9, 10), event: '事件4' },
    ];

    const height = 600;
    const width = 600;

    const svg = d3
      .select(timelineContainer.value)
      .append('svg')
      .attr('width', width)
      .attr('height', height);

    const eventDates = data.map(d => d.date);

    const yScale = d3
      .scaleTime()
      .domain(d3.extent(data, (d) => d.date) as [Date, Date])
      .range([height - 100, 10]);

    const axis = d3.axisLeft(yScale)
      .tickValues(eventDates)
      .tickFormat(timeFormat('%Y年%m月') as (domainValue: Date | d3.NumberValue, index: number) => string);
    svg.append('g').attr('transform', `translate(100, 0)`).call(axis);

    // 绘制事件点：调整cx为100 - 20（让圆在时间轴右侧，距离轴20px）
    svg
      .selectAll('circle')
      .data(data)
      .enter()
      .append('circle')
      .attr('cx', 100 + 20)
      .attr('cy', (d) => yScale(d.date))
      .attr('r', 4)
      .attr('fill', 'steelblue');

    // 绘制事件文本
    svg
      .selectAll('text.event-text')
      .data(data)
      .enter()
      .append('text')
      .attr('class', 'event-text')
      .attr('x', 100 + 30)
      .attr('y', (d) => yScale(d.date) + 5)
      .text((d) => d.event)
      .attr('text-anchor', 'start')
      .attr('fill', 'black')
      .attr('font-size', '12px');
  }
});
</script>

<style scoped>
.timeline-container {
  width: 100%;
  height: 650px;
  margin: 20px 0;
}
</style>
