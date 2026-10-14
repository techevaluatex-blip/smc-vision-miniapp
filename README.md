
 //@version=6
indicator("Robson SMC ICT", overlay=true, max_lines_count=100, max_boxes_count=50)

swingLen = input.int(5, "Swing Length", minval=2)
showFVG = input.bool(true, "Show Fair Value Gaps")
showStructure = input.bool(true, "Show BOS / CHoCH")
showSignals = input.bool(true, "Show Buy / Sell Signals")

ph = ta.pivothigh(high, swingLen, swingLen)
pl = ta.pivotlow(low, swingLen, swingLen)

var float lastHigh = na
var float lastLow = na
var int trend = 0

if not na(ph)
    lastHigh := ph

if not na(pl)
    lastLow := pl

bullBreak = not na(lastHigh) and ta.crossover(close, lastHigh)
bearBreak = not na(lastLow) and ta.crossunder(close, lastLow)

bullCHoCH = bullBreak and trend == -1
bearCHoCH = bearBreak and trend == 1

if bullBreak
    trend := 1

if bearBreak
    trend := -1

plotshape(showStructure and bullBreak, title="Bullish BOS/CHoCH", style=shape.labelup, location=location.belowbar, color=color.green, text="BOS", textcolor=color.white, size=size.tiny)
plotshape(showStructure and bearBreak, title="Bearish BOS/CHoCH", style=shape.labeldown, location=location.abovebar, color=color.red, text="BOS", textcolor=color.white, size=size.tiny)

plotshape(showStructure and bullCHoCH, title="Bullish CHoCH", style=shape.labelup, location=location.belowbar, color=color.aqua, text="CHoCH", textcolor=color.black, size=size.tiny)
plotshape(showStructure and bearCHoCH, title="Bearish CHoCH", style=shape.labeldown, location=location.abovebar, color=color.orange, text="CHoCH", textcolor=color.black, size=size.tiny)

bullFVG = low > high[2]
bearFVG = high < low[2]

if showFVG and bullFVG
    box.new(bar_index - 2, low, bar_index, high[2], bgcolor=color.new(color.green, 85), border_color=color.new(color.green, 55))

if showFVG and bearFVG
    box.new(bar_index - 2, low[2], bar_index, high, bgcolor=color.new(color.red, 85), border_color=color.new(color.red, 55))

buySignal = bullBreak and close > open
sellSignal = bearBreak and close < open

plotshape(showSignals and buySignal, title="Buy", style=shape.labelup, location=location.belowbar, color=color.lime, text="BUY", textcolor=color.black, size=size.small)
plotshape(showSignals and sellSignal, title="Sell", style=shape.labeldown, location=location.abovebar, color=color.red, text="SELL", textcolor=color.white, size=size.small)

alertcondition(buySignal, title="SMC Buy Alert", message="Bullish structure break on {{ticker}}")
alertcondition(sellSignal, title="SMC Sell Alert", message="Bearish structure break on {{ticker}}")
