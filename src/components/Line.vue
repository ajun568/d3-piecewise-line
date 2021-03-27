<template>
  <div style="padding: 20px;">
    <div id="line"></div>
  </div>
</template>

<script>
// tips: 需要结合业务需求做好边界值处理, 包括显示问题, 移动端适配问题(画布减小导致的空间减小), 不同数据情况的美观程度等;
// 欢迎各路大神对此代码提出建议, 欢迎留言指教.
import * as d3 from 'd3'
export default {
  name: 'd3-line',
  data() {
    return {}
  },
  mounted() {
    this.line()
  },
   watch: {
    options: {
      deep: true,
      handler() {
        this.line('update')
      }
    }
  },
  methods: {
    line(type) {
      // 数据模拟 - 真实场景中根据需求写成props, 暴露成对象进行配置, 做好初始化的处理
      const width = 800 // 画布宽度
      const height = 400 // 画布高度
      const background = '#1e2730' // 画布背景颜色
      const padding = { top: 40, right: 40, bottom: 50, left: 70 } // 画布留白
      const arrowPathX = 'M2,2 L10,6 L2,10 L4,6 Z' // 箭头路径
      const markerR = 15 // marker半径
      const axisColor = '#fff' // 坐标轴颜色
      const yAxisTofixed = 0 // y轴数据处理刻度
      const leaveLineWidth = 10 // 坐标轴交叉点位移
      const circleR = 6 // 坐标点直径
      const circleStrokeWidth = 2 // 坐标点描边宽度
      const circleColor = '#00d5bd' // 坐标点颜色
      const lineColor = '#00d5bd' // 路径颜色
      const lineWidth = 2.5 // 路径宽度
      const dottedWidth = 0.5 // 虚线宽度
      const dottedColor = '#fed7d7' // 虚线颜色
      const tooltipLineColor = '#697289' // tooltip虚线颜色
      
      const dataset = [ // 数据
        {
          xValue: 20,
          yValue: 0.3,
          filled: true,
        },
        {
          xValue: 20,
          yValue: 0.7,
          filled: false,
        },
        {
          xValue: 100,
          yValue: 0.3,
          filled: true,
        },
      ]

      const areaWidth = width - padding.left - padding.right
      const areaHeight = height - padding.top - padding.bottom

      // 数组去重
      const unique = (arr) => Array.from(new Set(arr))

      // 坐标轴数据处理
      let xData = dataset.map(item => item.xValue) || []
      xData = unique(xData)
      let yData = dataset.map(item => item.yValue) || []
      yData = unique(yData)

      // 份数计算
      const length = dataset.length <= 3 ? dataset.length / 2 : dataset.length // 单纯为了让当前展示数据好看些😁
      const partDistance = (d3.max(xData) - d3.min(xData)) / (length > 10 ? 10 : length)

      // 更新操作
      if (type === 'update') {
        d3.selectAll('.line').remove()
      }

      // 创建画布
      let svg = d3
        .select('#line')
        .attr('style', 'position: relative;')
        .append('svg')
        .attr('class', 'line')
        .attr('width', width)
        .attr('height', height)
        .attr('style', `background: ${background};`)

      // 创建线性比例尺
      const xScale = d3
        .scaleLinear()
        .domain([
          d3.min(xData, item => {
            return item - partDistance
          }),
          d3.max(xData, item => {
            return item + partDistance
          })
        ])
        .rangeRound([0, areaWidth])

      // 创建x轴
      const xAxis = d3
        .axisBottom()
        .tickValues(xData)
        .tickFormat(d3.format(',.0f'))
        .scale(xScale)

      let xAxisGroup = svg
        .append('g')
        .attr('class', 'axis')
        .attr('transform', `translate(${padding.left}, ${height - padding.bottom})`)
        .call(xAxis)

      // 样式优化
      xAxisGroup.selectAll('text')
        .attr('y', 16)

      xAxisGroup.selectAll('line')
        .attr('stroke-width', 2)
        .attr('y2', 10)

      // 创建marker
      svg
        .append('defs')
        .append('marker')
        .attr('id', 'arrowX')
        .attr('markerUnits', 'strokeWidth')
        .attr('markerWidth', markerR)
        .attr('markerHeight', markerR)
        .attr('refX', 6)
        .attr('refY', 6)
        .append('path')
        .attr('d', arrowPathX)
        .attr('fill', axisColor)

      let xPath = d3.path()
      xPath.moveTo(0, 0)
      xPath.lineTo(areaWidth + leaveLineWidth, 0)

      svg
        .append('path')
        .attr('d', xPath)
        .attr('fill', 'none')
        .attr('stroke', axisColor)
        .attr('stroke-width', 2)
        .attr('transform', `translate(${padding.left - leaveLineWidth}, ${height - padding.bottom})`)
        .attr('marker-end', 'url(#arrowX)')

      // 绘制y轴
      const yDataMax = d3.max(yData)
      const yScale = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(yData, item => yDataMax > 0.5 ? item + 0.2 : item + 0.1)
        ])
        .rangeRound([areaHeight, 0])
        
      const yAxis = d3
        .axisLeft()
        .scale(yScale)
        .tickValues(yData)
        .tickFormat(item => `${(item * 100).toFixed(yAxisTofixed)}%`)

      let yAxisGroup = svg
        .append('g')
        .attr('class', 'axis')
        .attr('transform', `translate(${padding.left}, ${padding.top})`)
        .call(yAxis)

      yAxisGroup.selectAll('text')
        .attr('x', -16)

      yAxisGroup.selectAll('line')
        .attr('stroke-width', 2)
        .attr('x2', -10)

      svg
        .append('defs')
        .append('marker')
        .attr('id', 'arrowY')
        .attr('markerUnits', 'strokeWidth')
        .attr('markerWidth', markerR)
        .attr('markerHeight', markerR)
        .attr('refX', 6)
        .attr('refY', 6)
        .attr('orient', '-90deg')
        .append('path')
        .attr('d', arrowPathX)
        .attr('fill', axisColor)

      let yPath = d3.path()
      yPath.moveTo(0, 0)
      yPath.lineTo(0, -(areaHeight + leaveLineWidth))

      svg
        .append('path')
        .attr('d', yPath)
        .attr('fill', 'none')
        .attr('stroke', axisColor)
        .attr('stroke-width', 2)
        .attr('transform', `translate(${padding.left}, ${height - padding.bottom + leaveLineWidth})`)
        .attr('marker-end', 'url(#arrowY)')

      // 绘制坐标点到坐标轴的虚线
      svg
        .append('g')
        .selectAll('.y-dotted')
        .data(dataset)
        .enter()
        .append('line')
        .attr('class', 'y-dotted')
        .attr('x1', d => xScale(d.xValue))
        .attr('y1', areaHeight)
        .attr('x2', d => xScale(d.xValue))
        .attr('y2', d => yScale(d.yValue))
        .style('stroke-dasharray', '3')
        .attr('stroke', dottedColor)
        .attr('stroke-width', `${dottedWidth}px`)
        .attr('transform', `translate(${padding.left}, ${padding.top})`)

      svg
        .append('g')
        .selectAll('.x-dotted')
        .data(dataset)
        .enter()
        .append('line')
        .attr('class', 'x-dotted')
        .attr('x1', d => xScale(d.xValue))
        .attr('y1', d => yScale(d.yValue))
        .attr('x2', 0)
        .attr('y2', d => yScale(d.yValue))
        .style('stroke-dasharray', '3')
        .attr('stroke', dottedColor)
        .attr('stroke-width', `${dottedWidth}px`)
        .attr('transform', `translate(${padding.left}, ${padding.top})`)

      // 折线数据处理
      let newDataset = []
      newDataset.push({
        xValue: dataset[0].xValue - partDistance,
        yValue: dataset[0].yValue,
        filled: true
      })
      dataset.forEach(item => item.filled ? newDataset.push(item) : newDataset.push(...[item, item]))
      newDataset.push({
        xValue: dataset[dataset.length - 1].xValue + partDistance,
        yValue: dataset[dataset.length - 1].yValue,
        filled: true
      })
      
      // 绘制折线
      svg.append('path')
        .datum(newDataset)
        .attr('fill', 'none')
        .attr('stroke', lineColor)
        .attr('stroke-width', lineWidth)
        .attr('class', 'line-solid')
        .attr(
          'd',
          d3.line()
            .x(d => xScale(d.xValue))
            .y(d => yScale(d.yValue))
            .defined((d, i) => {
              if (!i || i === newDataset.length - 1 || d.filled) return true
              if (d.xValue === newDataset[i + 1].xValue && d.xValue === newDataset[i - 1].xValue) return false
              return true
            })
        )
        .attr('transform', `translate(${padding.left}, ${padding.top})`)
      
      // 绘制坐标点
      svg
        .append('g')
        .selectAll('.dot')
        .data(dataset)
        .enter()
        .append('circle')
        .attr('transform', `translate(${padding.left}, ${padding.top})`)
        .attr('class', 'dot')
        .attr('cx', d => xScale(d.xValue))
        .attr('cy', d => yScale(d.yValue))
        .attr('r', circleR)
        .attr('fill', d => d.filled ? circleColor : background)
        .attr('stroke',circleColor)
        .attr('stroke-width', circleStrokeWidth)

      // 绘制tooltips
      let tooltip = d3
        .select('#line')
        .append('tooltip')
        .attr('class', 'tooltip')
        .style('position', 'absolute')
        .style('visibility', 'hidden')
        .style('pointer-events', 'none')
        .style('width', 'fit-content')
      
      tooltip
        .append('p')
        .attr('class', 'tooltip-title')
        .style('font-size', '16px')

      tooltip
        .append('p')
        .attr('class', 'tooltip-value')
        .style('font-size', '16px')
        .style('margin-top', '8px')

      // tooltip 虚线
      let xTooltipLine = d3
        .select('#line')
        .append('g')
        .attr('width', areaWidth)
        .attr('height', areaHeight)
        .style('position', 'absolute')
        .style('pointer-events', 'none')
        .style('top', `${padding.top}px`)
        .style('left', `${padding.left}px`)

      xTooltipLine
        .append('svg')
        .attr('width', areaWidth)
        .attr('height', areaHeight)
        .append('line')
        .attr('class', 'x-tooltip-line')
        .attr('x1', 0)
        .attr('x2', 0)
        .attr('y1', 0)
        .attr('y2', 0)
        .attr('fill', 'none')
        .attr('stroke', tooltipLineColor)
        .attr('stroke-width', '1px')
        .style('stroke-dasharray', ('3, 3'))

      let yTooltipLine = d3
        .select('#line')
        .append('g')
        .attr('width', areaWidth)
        .attr('height', areaHeight)
        .style('position', 'absolute')
        .style('pointer-events', 'none')
        .style('top', `${padding.top}px`)
        .style('left', `${padding.left}px`)

      yTooltipLine
        .append('svg')
        .attr('width', areaWidth)
        .attr('height', areaHeight)
        .append('line')
        .attr('class', 'y-tooltip-line')
        .attr('x1', 0)
        .attr('x2', 0)
        .attr('y1', 0)
        .attr('y2', 0)
        .attr('fill', 'none')
        .attr('stroke', tooltipLineColor)
        .attr('stroke-width', '1px')
        .style('stroke-dasharray', ('3, 3'))

      // 数据处理 - 补全首位项
      const dealDataset = [
        {
          xValue: dataset[0].xValue - partDistance,
          yValue: dataset[0].yValue,
        },
        ...dataset,
        {
          xValue: dataset[dataset.length - 1].xValue + partDistance,
          yValue: dataset[dataset.length - 1].yValue,
        }
      ]

      const mouseMove = d => {
        const bisect = d3.bisector(d => d.xValue).right
        const xInvert = xScale.invert(d.offsetX - padding.left)
        const i = bisect(dealDataset, xInvert, 1)
        let topContent = ''
        let bottomContent = ''
        let px = d.offsetX // x.position
        let py = 0 // y.position

        // tooltip 展示内容
        if (i - 1 <= 0) { // 首项
          topContent = `${dealDataset[i].dot ? '≤' : '<'} ${dealDataset[i].xValue}`
          bottomContent = `${(dealDataset[i].yValue * 100).toFixed(2)}%`
          py = yScale(dealDataset[i].yValue)
        }
        else if (i + 1 >= dealDataset.length) { // 尾项
          if (!dealDataset[dealDataset.length - 2]) return

          topContent = `${dealDataset[dealDataset.length - 2].dot ? '“≥' : '>'} ${dealDataset[dealDataset.length - 2].xValue}`
          bottomContent = `${(dealDataset[dealDataset.length - 2].yValue * 100).toFixed(2)}%`
          py = yScale(dealDataset[i - 1].yValue)
        }
        else { // 中间项
          if (!dealDataset[i - 1] || !dealDataset[i]) return

          let x0 = dealDataset[i - 1].xValue
          let x1 = dealDataset[i].xValue
          let y0 = dealDataset[i - 1].yValue
          let y1 = dealDataset[i].yValue

          // 计算y值 (x - x1) * (y2 - y1) = (x2 - x1) * (y - y1)
          let computedY = (x) => {
            return (x * (y1 - y0) + x1 * y0 - x0 * y1) / (x1 - x0)
          }

          topContent = `≈ ${xInvert.toFixed(0)}`
          bottomContent = `${(computedY(xInvert) * 100).toFixed(2)}%`
          py = yScale(computedY(xInvert))
        }

         // 特殊处理边缘位置
        const getBoundingClientRect = d3
          .select('.tooltip')
          .node()
          .getBoundingClientRect()

        const tipWidth = Math.ceil(getBoundingClientRect.width)
        const tipHeight = Math.ceil(getBoundingClientRect.height)

        // offsetX, offsetY
        let offsetX = d.offsetX - padding.left
        let offsetY = py
        if (tipWidth > d.offsetX) offsetX = d.offsetX
        if (tipWidth > width - d.offsetX) offsetX = width - tipWidth
        if (tipHeight > py) offsetY = tipHeight

        tooltip
          .select('.tooltip-title')
          .text(`x: ${topContent}`)

        tooltip
          .select('.tooltip-value')
          .text(`y: ${bottomContent}`)

        tooltip
          .style('top', `${offsetY + 10}px`)
          .style('left', `${offsetX}px`)

        xTooltipLine
          .selectAll('.x-tooltip-line')
          .attr('x1', 0)
          .attr('x2', px - padding.left)
          .attr('y1', py)
          .attr('y2', py)
        
        yTooltipLine
          .selectAll('.y-tooltip-line')
          .attr('x1', px - padding.left)
          .attr('x2', px - padding.left)
          .attr('y1', areaHeight)
          .attr('y2', py)
      }

      const mouseOver = () => {
        tooltip.style('visibility', 'visible')
        xTooltipLine.style('visibility', 'visible')
        yTooltipLine.style('visibility', 'visible')
      }

      const mouseOut = () => {
        tooltip.style('visibility', 'hidden')
        xTooltipLine.style('visibility', 'hidden')
        yTooltipLine.style('visibility', 'hidden')
      }

      // 绘制矩形做覆盖
      svg
        .append('rect')
        .attr('width', areaWidth)
        .attr('height', areaHeight)
        .style('fill', 'none')
        .style('pointer-events', 'all')
        .style('cursor', 'pointer')
        .attr('transform', `translate(${padding.left}, ${padding.top})`)
        .on('mouseover', mouseOver)
        .on('mouseout', mouseOut)
        .on('mousemove', mouseMove)
    }
  }
}
</script>

<style>
#line {
  width: 800px;
  height: 400px;
  overflow: hidden;
}
.axis {
  color: #fff;
  font-size: 14px;
  font-weight: 500;
}
.axis .domain {
  display: none;
}
.test {
  width: 150px;
  height: 85px;
  position: absolute;
  top: 160px;
  left: 70px;
  background-color: #F3F5F7;
  border-radius: 4px;
  padding: 10px;
}
.tooltip {
  background: rgba(0, 0, 0, .7);
  box-sizing: border-box;
  padding: 10px;
  border-radius: 4px;
  color: #fff;
}
</style>
