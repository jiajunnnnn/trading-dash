// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © salahbelidong

//-------------Original--------------
// autor original : @KP_House, @JustInNovel, @jdehorty 
// Remake @salahbelidong

//@version=5
indicator("Dashboard", overlay=true, scale=scale.none, max_lines_count=500)
//Start dashboard
import jdehorty/EconomicCalendar/1 as calendar

// ---- Table Settings Start ----//
max    = 160    //Maximum Length
min    = 10     //Minimum Length


// Input setting page start
dash_loc    = input.session("Bottom Right","Dashboard Posision"  ,["Top Right","Bottom Right","Top Left","Bottom Left", "Middle Right","Bottom Center"], group='Style Settings')
text_size   = input.session('Small',"Dashboard Size"  ,options=["Tiny","Small","Normal","Large"]  ,group='Style Settings')
cell_up     = input.color(color.green,'Up Cell Color'  ,group='Style Settings')
cell_dn     = input.color(color.red,'Down Cell Color'  ,group='Style Settings')
cell_Neut   = input.color(color.gray,'Nochange  Cell Color'  ,group='Style Settings')
row_col     = color.blue
col_col     = color.white
txt_col     = color.white
cell_transp = input.int(60,'Cell Transparency'  ,minval=0  ,maxval=100  ,group='Style Settings')

Header_col  = color.rgb(35, 94, 255)
//MACDV color
cell_MACDV1 = input.color(color.teal,'Continue/Reversal  ',      group='MACD COLOR')
cell_MACDV2 = input.color(color.green,'Buy G0 ',      group='MACD COLOR')
cell_MACDV3 = input.color(color.red,'Buy Retest  ',      group='MACD COLOR')
cell_MACDV4 = input.color(color.rgb(194, 179, 47),'Sideway ',      group='MACD COLOR')
cell_MACDV5 = input.color(color.green,'Short go',     group='MACD COLOR')
cell_MACDV6 = input.color(color.red,'Short Retest  '  ,    group='MACD COLOR')
cell_MACDV7 = input.color(color.rgb(204, 8, 24),'Wait Continue/Reversal ',  group='MACD COLOR')
//Momentum color
cell_phase1 = input.color(color.green,'Phase1:Accumulation',    group='Momentum')
cell_phase2 = input.color(color.teal,'Phase2:Runing'  ,        group='Momentum')
cell_phase3 = input.color(color.red,'Phase3:Re-Accumulation' ,          group='Momentum')
cell_phase4 = input.color(color.red,'Phase4:Distribution'  ,    group='Momentum')
cell_phase5 = input.color(color.orange,'Phase5:Bearish'  ,      group='Momentum')
cell_phase6 = input.color(color.green,'Phase6:Rev/Continue'  ,      group='Momentum')
// ---- Table Settings End ----}//

// ---- Indicators Show/Hide Settings Start ----//

showCls     = input.bool(defval=true, title="Show Price Close",     group="Colum On/Off")
showMA01    = input.bool(defval=true, title="Show MA01",            group="Colum On/Off")
showMA02    = input.bool(defval=true, title="Show MA02",            group="Colum On/Off")
showMACross = input.bool(defval=true, title="Show Trend",           group="Colum On/Off")
showRSI     = input.bool(defval=true, title="Show RSI ",            group="Colum On/Off")
showMACDV   = input.bool(defval=true, title="Show MACDV",           group="Colum On/Off")
showSignalV = input.bool(defval=true, title="Show SignalV",         group="Colum On/Off")
showMACDV_Status = input.bool(defval=true, title="Show Condition",  group="Colum On/Off")
showmomentum = input.bool(defval=true, title="Show Momentum",       group="Colum On/Off")

//---- MACD-V code start ----//
MACD_fast_length    = input(title="MACD-V Fast", defval=14, group="MACD-V Settings")
MACD_slow_length    = input(title="MACD-V Slow", defval=26, group="MACD-V Settings")
MACD_signal_length  = input.int(title="MACD-V Signal ",  minval = 1, maxval = 50, defval = 9, group="MACD-V Settings")
MACD_atr_length     = input(title="ATR", defval=26, group="MACD-V Settings")

// ---- Indicators Show/Hide Settings end ----}//


// ==================
// ==== Settings ====
// ==================


//------Seting Color Calender Economi------

color1 = color.red
color2 = color.orange
color3 = color.yellow
color4 = color.lime
color5 = color.aqua
color6 = color.fuchsia
color7 = color.silver


show_fomc_meetings = input.bool(defval = false, title = "📅 FOMC", inline = "FOMC", group="⚙️ Settings", tooltip="The FOMC meets eight times a year to determine the course of monetary policy. The FOMC's decisions are announced in a press release at 2:15 p.m. ET on the day of the meeting. The press release is followed by a press conference at 2:30 p.m. ET. The FOMC's decisions are based on a review of economic and financial developments and its assessment of the likely effects of these developments on the economic outlook.")
c_fomcMeeting = input.color(color.new(color1, 50), title = "Color", group="⚙️ Settings", inline = "FOMC")

show_fomc_minutes = input.bool(defval = false, title = "📅 FOMC Minutes", inline = "FOMCMinutes", group="⚙️ Settings", tooltip="The FOMC minutes are released three weeks after each FOMC meeting. The minutes provide a detailed account of the FOMC's discussion of economic and financial developments and its assessment of the likely effects of these developments on the economic outlook.")
c_fomcMinutes = input.color(color.new(color2, 50), title = "Color", group="⚙️ Settings", inline = "FOMCMinutes")

show_ppi = input.bool(defval = false, title = "📅 Producer Price Index (PPI)", inline = "PPI", group="⚙️ Settings", tooltip="The Producer Price Index (PPI) measures changes in the price level of goods and services sold by domestic producers. The PPI is a weighted average of prices of a basket of goods and services, such as transportation, food, and medical care. The PPI is a leading indicator of CPI.")
c_ppi = input.color(color.new(color3, 50), title = "Color", group="⚙️ Settings", inline = "PPI")

show_cpi = input.bool(defval = false, title = "📅 Consumer Price Index (CPI)", inline = "CPI", group="⚙️ Settings", tooltip="The Consumer Price Index (CPI) measures changes in the price level of goods and services purchased by households. The CPI is a weighted average of prices of a basket of consumer goods and services, such as transportation, food, and medical care. The CPI-U is the most widely used measure of inflation. The CPI-U is based on a sample of about 87,000 households and measures the change in the cost of a fixed market basket of goods and services purchased by urban consumers.")
c_cpi = input.color(color.new(color4, 50), title = "Color", group="⚙️ Settings", inline = "CPI")

show_csi = input.bool(defval = false, title = "📅 Consumer Sentiment Index (CSI)", inline = "CSI", group="⚙️ Settings", tooltip="The University of Michigan's Consumer Sentiment Index (CSI) is a measure of consumer attitudes about the economy. The CSI is based on a monthly survey of 500 U.S. households. The index is based on consumers' assessment of present and future economic conditions. The CSI is a leading indicator of consumer spending, which accounts for about two-thirds of U.S. economic activity.")
c_csi = input.color(color.new(color5, 50), title = "Color", group="⚙️ Settings", inline = "CSI")

show_cci = input.bool(defval = false, title = "📅 Consumer Confidence Index (CCI)", inline = "CCI", group="⚙️ Settings", tooltip="The Conference Board's Consumer Confidence Index (CCI) is a measure of consumer attitudes about the economy. The CCI is based on a monthly survey of 5,000 U.S. households. The index is based on consumers' assessment of present and future economic conditions. The CCI is a leading indicator of consumer spending, which accounts for about two-thirds of U.S. economic activity.")
c_cci = input.color(color.new(color6, 50), title = "Color", group="⚙️ Settings", inline = "CCI")

show_nfp = input.bool(defval = false, title = "📅 Non-Farm Payroll (NFP)", inline = "NFP", group="⚙️ Settings", tooltip="The Non-Farm Payroll (NFP) is a measure of the change in the number of employed persons, excluding farm workers and government employees. The NFP is a leading indicator of consumer spending, which accounts for about two-thirds of U.S. economic activity.")
c_nfp = input.color(color.new(color7, 50), title = "Color", group="⚙️ Settings", inline = "NFP")

show_legend = input.bool(true, "Show Legend", group="⚙️ Settings", inline = "Legend", tooltip="Show the color legend for the economic calendar events.")


// =======================
// ==== Dates & Times ====
// =======================

getUnixTime(_eventArr, _index) => 
    switch 
        timeframe.isdaily => array.get(_eventArr, _index) - timeframe.multiplier*86400000 // -n day(s)
        timeframe.isweekly => array.get(_eventArr, _index) - timeframe.multiplier*604800000 // -n week(s)
        timeframe.ismonthly => array.get(_eventArr, _index) - timeframe.multiplier*2592000000 // -n month(s)
        timeframe.isminutes and timeframe.multiplier > 59 => array.get(_eventArr, _index) - timeframe.multiplier*60000 // -n minute(s)
        => array.get(_eventArr, _index)

if barstate.islastconfirmedhistory

    // Note: An offset of -n units is needed to realign events with the timeframe in which they occurred
    if show_fomc_meetings
        fomcMeetingsArr = calendar.fomcMeetings()
        for i = 0 to array.size(fomcMeetingsArr) - 1
            unixTime = getUnixTime(fomcMeetingsArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_fomcMeeting, width=2, xloc=xloc.bar_time)

    if show_fomc_minutes
        fomcMinutesArr = calendar.fomcMinutes()
        for i = 0 to array.size(fomcMinutesArr) - 1
            unixTime = getUnixTime(fomcMinutesArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_fomcMinutes, width=2, xloc=xloc.bar_time)

    if show_ppi
        ppiArr = calendar.ppiReleases()
        for i = 0 to array.size(ppiArr) - 1
            unixTime = getUnixTime(ppiArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_ppi, width=2, xloc=xloc.bar_time)

    if show_cpi
        cpiArr = calendar.cpiReleases()
        for i = 0 to array.size(cpiArr) - 1
            unixTime = getUnixTime(cpiArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_cpi, width=2, xloc=xloc.bar_time)
    
    if show_csi
        csiArr = calendar.csiReleases()
        for i = 0 to array.size(csiArr) - 1
            unixTime = getUnixTime(csiArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_csi, width=2, xloc=xloc.bar_time)
    
    if show_cci
        cciArr = calendar.cciReleases()
        for i = 0 to array.size(cciArr) - 1
            unixTime = getUnixTime(cciArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_cci, width=2, xloc=xloc.bar_time)
    
    if show_nfp
        nfpArr = calendar.nfpReleases()
        for i = 0 to array.size(nfpArr) - 1
            unixTime = getUnixTime(nfpArr, i)
            line.new(x1=unixTime, y1=high, x2=unixTime, y2=low, extend=extend.both,color=c_nfp, width=2, xloc=xloc.bar_time)

// ================
// ==== Legend ====
// ================
if show_legend
    var tbl = table.new(position.top_right, columns=8, rows=8, frame_color=#151715, frame_width=1, border_width=2, border_color=color.new(color.black, 100))
    units = timeframe.isminutes ? "m" : ""
    table.cell(tbl, 1, 0, syminfo.ticker + ' => ' + str.tostring(timeframe.period)+ units, text_halign=text.align_center, text_color=color.gray, text_size=size.small)
    table.cell(tbl, 2, 0, 'FOMC Meeting', text_halign=text.align_center, bgcolor=color.black, text_color=color1, text_size=size.small)
    table.cell(tbl, 3, 0, 'FOMC Minutes', text_halign=text.align_center, bgcolor=color.black, text_color=color2, text_size=size.small)
    table.cell(tbl, 4, 0, 'Producer Price Index (PPI)', text_halign=text.align_center, bgcolor=color.black, text_color=color3, text_size=size.small)
    table.cell(tbl, 1, 1, 'Consumer Price Index (CPI)', text_halign=text.align_center, bgcolor=color.black, text_color=color4, text_size=size.small)
    table.cell(tbl, 2, 1, 'Consumer Sentiment Index (CSI)', text_halign=text.align_center, bgcolor=color.black, text_color=color5, text_size=size.small)
    table.cell(tbl, 3, 1, 'Consumer Confidence Index (CCI)', text_halign=text.align_center, bgcolor=color.black, text_color=color6, text_size=size.small)
    table.cell(tbl, 4, 1, 'Non-Farm Payrolls (NFP)', text_halign=text.align_center, bgcolor=color.black, text_color=color7, text_size=size.small)

// =======================
// ==== CE And ===========
// =======================


// ---- Timeframe Row Show/Hide Settings Start ----//
showTF1 = input.bool(defval=true, title="Show TF MN", inline='indicator1',group="Rows Settings")

f_MACDV(_close) =>

    //---- Indicators code Start ----//
    CLS= _close[1]

    //---- RSI code start ----//
    rsiPeriod   = 14
    RSI         = ta.rsi(_close, rsiPeriod)

    //---- RSI code end ----//

    //---- EMA 1 code start----//
    length_MA1 = input.int(title="MA1",defval=50, minval=1)
    MA1        = ta.ema(_close, length_MA1)
    //plot(MA01, color=color.red, title="MA1")
    //---- EMA 1  code end ----//

    //---- EMA 2 code start---//
    length_MA2 = input.int(title="MA2",defval=200, minval=1)
    MA2        = ta.ema(_close, length_MA2)
    //plot(MA02, color=color.blue, title="MA2")
    //---- EMA 2  code end ----//

    // Input seeting page end
    // Calculating 
    fast_ma =  ta.ema(_close, MACD_fast_length)
    slow_ma =  ta.ema(_close, MACD_slow_length)
    atr     =  ta.atr(MACD_atr_length)
    MACDV   = (((fast_ma - slow_ma)/atr)*100)//[( 12 bar EMA - 26 bar EMA) / ATR(26) ] * 100
    SignalV = ta.ema(MACDV, MACD_signal_length)
    //---- MACD-V code end ----//

    //---- Indicators code end ----//


    //-----Condition start
    stringmacdv     =(MACDV>150) ? "Wait Continue/Reversal" :(MACDV>50 and MACDV<150 and MACDV>SignalV ) ? "Buy G0" :(MACDV>50 and MACDV<150 and MACDV<SignalV ) ? "Buy Retest":(MACDV<50) and (MACDV>-50) ? "Sideway" :(MACDV<-50 and MACDV>-150 and MACDV>SignalV ) ? "Short go":(MACDV<-50 and MACDV>-150 and MACDV<SignalV ) ? "Short Retest":(MACDV<150) ? "Wait Continue/Reversal" :na
    //momentum
    stringmomentum  =(CLS>MA1 and CLS>MA2 and MA1<MA2) ? "Accumulation:Stop Sell - Setup Buy" :(CLS>MA1 and CLS>MA2 and MA1>MA2) ? "Runing Up: Buy Runing":(CLS<MA1 and CLS>MA2 and MA1>MA2) ? "Re-Acumulasi: Continue Up":(CLS<MA1 and CLS<MA2 and MA1>MA2) ? "Distribution: Stop Buy-Setup Short":(CLS<MA1 and CLS<MA2 and MA1<MA2) ? "Re-Distribusi: Continue Down":(CLS>MA1 and CLS<MA2 and MA1<MA2) ? "Accumulation-Distribusi: Don't Trade Wait Break":na
        
    //-----Condition end

    // Return values
    [CLS, MA1, MA2, RSI, MACDV, SignalV, stringmacdv, stringmomentum]

// ] -------- Alerts ----------------- [


//---- Table Position & Size code start {----//
var table_position = dash_loc == 'Bottom Right' ? position.bottom_right :
  dash_loc == 'Bottom Left' ? position.bottom_left :
  dash_loc == 'Middle Right' ? position.middle_right :
  dash_loc == 'Bottom Center' ? position.bottom_center :
  dash_loc == 'Top Left' ? position.top_right : position.bottom_right
  
var table_text_size = text_size == 'Normal' ? size.normal :
  text_size == 'Tiny' ? size.tiny :
  text_size == 'Small' ? size.small :
  text_size == 'Normal' ? size.normal : size.large

var t = table.new(table_position,15,math.abs(max-min)+2,
  frame_color   =color.new(#f1ff2a, 0),
  frame_width   =1,
  border_color  =color.new(#f1ff2a,0),
  border_width  =1)
//---- Table Position & Size code end ----//

// get values for table

[CLS_chart, MA1_chart, MA2_chart, RSI_chart, MACDV_chart, SignalV_chart, stringmacdv_chart, stringmomentum_chart] = f_MACDV(close)
[CLS_5_min, MA1_5_min, MA2_5_min, RSI_5_min, MACDV_5_min, SignalV_5_min, stringmacdv_5_min, stringmomentum_5_min] = request.security(syminfo.tickerid, "5", f_MACDV(close), lookahead=barmerge.lookahead_on)
[CLS_15_min, MA1_15_min, MA2_15_min, RSI_15_min, MACDV_15_min, SignalV_15_min, stringmacdv_15_min, stringmomentum_15_min] = request.security(syminfo.tickerid, "15", f_MACDV(close), lookahead=barmerge.lookahead_on)
[CLS_1_hour, MA1_1_hour, MA2_1_hour, RSI_1_hour, MACDV_1_hour, SignalV_1_hour, stringmacdv_1_hour, stringmomentum_1_hour] = request.security(syminfo.tickerid, "60", f_MACDV(close), lookahead=barmerge.lookahead_on)
[CLS_4_hour, MA1_4_hour, MA2_4_hour, RSI_4_hour, MACDV_4_hour, SignalV_4_hour, stringmacdv_4_hour, stringmomentum_4_hour] = request.security(syminfo.tickerid, "240", f_MACDV(close), lookahead=barmerge.lookahead_on)
[CLS_1_day, MA1_1_day, MA2_1_day, RSI_1_day, MACDV_1_day, SignalV_1_day, stringmacdv_1_day, stringmomentum_1_day] = request.security(syminfo.tickerid, "D", f_MACDV(close), lookahead=barmerge.lookahead_on)


//---- Table Column & Rows code start ----//
if (barstate.islast)
    //---- Table Main Column Headers code start ----//
    table.cell(t,1,1,'TimeFrem',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showCls
        table.cell(t,2,1,'L.Close',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showMA01
        table.cell(t,3,1,'MA01',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showMA02
        table.cell(t,4,1,'MA02',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showMACross
        table.cell(t,5,1,'Trend',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showRSI
        table.cell(t,6,1,'RSI',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showMACDV
        table.cell(t,7,1,'MACDV',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showSignalV
        table.cell(t,8,1,'SignalV',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showMACDV_Status
        table.cell(t,9,1,'Condition',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)
    if showmomentum
        table.cell(t,10,1,'Phase Market',text_color=col_col,text_size=table_text_size,bgcolor=Header_col)  

    //---- Table Main Column Headers code end ----//
 
    //---- Display data code start ----//

    //---------------------- Chart period ----------------------------------

    table.cell(t,1,2, "Chart",text_color=color.white,text_size=table_text_size, bgcolor=color.rgb(0, 68, 255))
    if  showCls
        table.cell(t,2,2, str.tostring(CLS_chart, '#.###'),text_color=color.new(CLS_chart >CLS_chart[2] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(CLS_chart >CLS_chart[2] ? cell_up : cell_dn ,cell_transp))
    if  showMA01
        table.cell(t,3,2, str.tostring(MA1_chart, '#.###'),text_color=color.new(MA1_chart >MA1_chart[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_chart >MA1_chart[1]  ? cell_up : cell_dn ,cell_transp))
    if  showMA02
        table.cell(t,4,2, str.tostring(MA2_chart, '#.###'),text_color=color.new(MA2_chart >MA2_chart[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA2_chart >MA2_chart[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACross
        table.cell(t,5,2, MA1_chart > MA2_chart ? "Bullish" : "Bearish",text_color=color.new(MA1_chart > MA2_chart ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_chart > MA2_chart ? cell_up : cell_dn ,cell_transp))
    if  showRSI
        table.cell(t,6,2, str.tostring(RSI_chart, '#.###'),text_color=color.new(RSI_chart > 50 ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(RSI_chart > 50 ? cell_up : cell_dn ,cell_transp))
    if  showMACDV
        table.cell(t,7,2,str.tostring(MACDV_chart, '#.###'),text_color=color.new(MACDV_chart > MACDV_chart[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MACDV_chart > MACDV_chart[1] ? cell_up : cell_dn ,cell_transp))
    if  showSignalV
        table.cell(t,8,2,str.tostring(SignalV_chart, '#.###'),text_color=color.new(SignalV_chart > SignalV_chart[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(SignalV_chart> SignalV_chart[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACDV_Status
        table.cell(t,9,2,stringmacdv_chart,text_color=color.rgb(0, 0, 0),text_size=table_text_size, bgcolor=color.new(MACDV_chart>50 ? cell_up :MACDV_chart<-50 ?  cell_dn:cell_MACDV4  ,cell_transp)) 
    if  showmomentum
        table.cell(t,10,2,stringmomentum_chart,text_color=color.rgb(2, 2, 2),text_size=table_text_size, bgcolor=color.new(CLS_chart>MA1_chart and CLS_chart>MA2_chart and MA1_chart<MA2_chart ? cell_phase1 : (CLS_chart>MA1_chart and CLS_chart>MA2_chart and MA1_chart>MA2_chart) ? cell_phase2 : (CLS_chart<MA1_chart and CLS_chart>MA2_chart and MA1_chart>MA2_chart) ?cell_phase3 :(CLS_chart<MA1_chart and CLS_chart<MA2_chart and MA1_chart>MA2_chart) ? cell_phase4:(CLS_chart<MA1_chart and CLS_chart<MA2_chart and MA1_chart<MA2_chart) ? cell_phase5:(CLS_chart>MA1_chart and CLS_chart<MA2_chart and MA1_chart<MA2_chart) ? cell_phase6:col_col,cell_transp))

 //   alert("\nRSI =(" + str.tostring(CLS_chart, '#.###') + ")\n Momentum = (" + str.tostring(stringmomentum_chart) +  ")\n Trend =("+ str.tostring(MA1_chart > MA2_chart ? "Bullish" : "Bearish")+").", alert.freq_once_per_bar_close)
       


//---------------------- 5 minute chart ----------------------------------

    table.cell(t,1,3, "5 minute",text_color=color.white,text_size=table_text_size, bgcolor=color.rgb(0, 68, 255))
    if  showCls
        table.cell(t,2,3, str.tostring(CLS_5_min, '#.###'),text_color=color.new(CLS_5_min >CLS_5_min[2] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(CLS_5_min >CLS_5_min[2] ? cell_up : cell_dn ,cell_transp))
    if  showMA01
        table.cell(t,3,3, str.tostring(MA1_5_min, '#.###'),text_color=color.new(MA1_5_min >MA1_5_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_5_min >MA1_5_min[1]  ? cell_up : cell_dn ,cell_transp))
    if  showMA02
        table.cell(t,4,3, str.tostring(MA2_5_min, '#.###'),text_color=color.new(MA2_5_min >MA2_5_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA2_5_min >MA2_5_min[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACross
        table.cell(t,5,3, MA1_5_min > MA2_5_min ? "Bullish" : "Bearish",text_color=color.new(MA1_5_min > MA2_5_min ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_5_min > MA2_5_min ? cell_up : cell_dn ,cell_transp))
    if  showRSI
        table.cell(t,6,3, str.tostring(RSI_5_min, '#.###'),text_color=color.new(RSI_5_min > 50 ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(RSI_5_min > 50 ? cell_up : cell_dn ,cell_transp))
    if  showMACDV
        table.cell(t,7,3,str.tostring(MACDV_5_min, '#.###'),text_color=color.new(MACDV_5_min > MACDV_5_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MACDV_5_min > MACDV_5_min[1] ? cell_up : cell_dn ,cell_transp))
    if  showSignalV
        table.cell(t,8,3,str.tostring(SignalV_5_min, '#.###'),text_color=color.new(SignalV_5_min > SignalV_5_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(SignalV_5_min> SignalV_5_min[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACDV_Status
        table.cell(t,9,3,stringmacdv_5_min,text_color=color.rgb(5, 5, 5),text_size=table_text_size, bgcolor=color.new(MACDV_5_min>50 ? cell_up :MACDV_5_min<-50 ?  cell_dn:cell_MACDV4  ,cell_transp)) 
    if  showmomentum
        table.cell(t,10,3,stringmomentum_5_min,text_color=color.rgb(5, 5, 5),text_size=table_text_size, bgcolor=color.new(CLS_5_min>MA1_5_min and CLS_5_min>MA2_5_min and MA1_5_min<MA2_5_min ? cell_phase1 : (CLS_5_min>MA1_5_min and CLS_5_min>MA2_5_min and MA1_5_min>MA2_5_min) ? cell_phase2 : (CLS_5_min<MA1_5_min and CLS_5_min>MA2_5_min and MA1_5_min>MA2_5_min) ?cell_phase3 :(CLS_5_min<MA1_5_min and CLS_5_min<MA2_5_min and MA1_5_min>MA2_5_min) ? cell_phase4:(CLS_5_min<MA1_5_min and CLS_5_min<MA2_5_min and MA1_5_min<MA2_5_min) ? cell_phase5:(CLS_5_min>MA1_5_min and CLS_5_min<MA2_5_min and MA1_5_min<MA2_5_min) ? cell_phase6:col_col,cell_transp))


       

//---------------------- 15 minute chart ----------------------------------

    table.cell(t,1,4, "15 minute",text_color=color.rgb(245, 243, 243),text_size=table_text_size, bgcolor=color.rgb(0, 68, 255))
    if  showCls
        table.cell(t,2,4, str.tostring(CLS_15_min, '#.###'),text_color=color.new(CLS_15_min >CLS_15_min[2] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(CLS_15_min >CLS_15_min[2] ? cell_up : cell_dn ,cell_transp))
    if  showMA01
        table.cell(t,3,4, str.tostring(MA1_15_min, '#.###'),text_color=color.new(MA1_15_min >MA1_15_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_15_min >MA1_15_min[1]  ? cell_up : cell_dn ,cell_transp))
    if  showMA02
        table.cell(t,4,4, str.tostring(MA2_15_min, '#.###'),text_color=color.new(MA2_15_min >MA2_15_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA2_15_min >MA2_15_min[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACross
        table.cell(t,5,4, MA1_15_min > MA2_15_min ? "Bullish" : "Bearish",text_color=color.new(MA1_15_min > MA2_15_min ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_15_min > MA2_15_min ? cell_up : cell_dn ,cell_transp))
    if  showRSI
        table.cell(t,6,4, str.tostring(RSI_15_min, '#.###'),text_color=color.new(RSI_15_min > 50 ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(RSI_15_min > 50 ? cell_up : cell_dn ,cell_transp))
    if  showMACDV
        table.cell(t,7,4,str.tostring(MACDV_15_min, '#.###'),text_color=color.new(MACDV_15_min > MACDV_15_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MACDV_15_min > MACDV_15_min[1] ? cell_up : cell_dn ,cell_transp))
    if  showSignalV
        table.cell(t,8,4,str.tostring(SignalV_15_min, '#.###'),text_color=color.new(SignalV_15_min > SignalV_15_min[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(SignalV_15_min> SignalV_15_min[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACDV_Status
        table.cell(t,9,4,stringmacdv_15_min,text_color=color.rgb(7, 7, 7),text_size=table_text_size, bgcolor=color.new(MACDV_15_min>50 ? cell_up :MACDV_15_min<-50 ?  cell_dn:cell_MACDV4  ,cell_transp)) 
    if  showmomentum
        table.cell(t,10,4,stringmomentum_15_min,text_color=color.rgb(7, 7, 7),text_size=table_text_size, bgcolor=color.new(CLS_15_min>MA1_15_min and CLS_15_min>MA2_15_min and MA1_15_min<MA2_15_min ? cell_phase1 : (CLS_15_min>MA1_15_min and CLS_15_min>MA2_15_min and MA1_15_min>MA2_15_min) ? cell_phase2 : (CLS_15_min<MA1_15_min and CLS_15_min>MA2_15_min and MA1_15_min>MA2_15_min) ?cell_phase3 :(CLS_15_min<MA1_15_min and CLS_15_min<MA2_15_min and MA1_15_min>MA2_15_min) ? cell_phase4:(CLS_15_min<MA1_15_min and CLS_15_min<MA2_15_min and MA1_15_min<MA2_15_min) ? cell_phase5:(CLS_15_min>MA1_15_min and CLS_15_min<MA2_15_min and MA1_15_min<MA2_15_min) ? cell_phase6:col_col,cell_transp))


//---------------------- 1 Hour chart ----------------------------------

    table.cell(t,1,6, "1 Hour",text_color=color.white,text_size=table_text_size, bgcolor=color.rgb(0, 68, 255))
    if  showCls
        table.cell(t,2,6, str.tostring(CLS_1_hour, '#.###'),text_color=color.new(CLS_1_hour >CLS_1_hour[2] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(CLS_1_hour >CLS_1_hour[2] ? cell_up : cell_dn ,cell_transp))
    if  showMA01
        table.cell(t,3,6, str.tostring(MA1_1_hour, '#.###'),text_color=color.new(MA1_1_hour >MA1_1_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_1_hour >MA1_1_hour[1]  ? cell_up : cell_dn ,cell_transp))
    if  showMA02
        table.cell(t,4,6, str.tostring(MA2_1_hour, '#.###'),text_color=color.new(MA2_1_hour >MA2_1_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA2_1_hour >MA2_1_hour[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACross
        table.cell(t,5,6, MA1_1_hour > MA2_1_hour ? "Bullish" : "Bearish",text_color=color.new(MA1_1_hour > MA2_1_hour ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_1_hour > MA2_1_hour ? cell_up : cell_dn ,cell_transp))
    if  showRSI
        table.cell(t,6,6, str.tostring(RSI_1_hour, '#.###'),text_color=color.new(RSI_1_hour > 50 ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(RSI_1_hour > 50 ? cell_up : cell_dn ,cell_transp))
    if  showMACDV
        table.cell(t,7,6,str.tostring(MACDV_1_hour, '#.###'),text_color=color.new(MACDV_1_hour > MACDV_1_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MACDV_1_hour > MACDV_1_hour[1] ? cell_up : cell_dn ,cell_transp))
    if  showSignalV
        table.cell(t,8,6,str.tostring(SignalV_1_hour, '#.###'),text_color=color.new(SignalV_1_hour > SignalV_1_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(SignalV_1_hour> SignalV_1_hour[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACDV_Status
        table.cell(t,9,6,stringmacdv_1_hour,text_color=color.rgb(7, 7, 7),text_size=table_text_size, bgcolor=color.new(MACDV_1_hour>50 ? cell_up :MACDV_1_hour<-50 ?  cell_dn:cell_MACDV4  ,cell_transp)) 
    if  showmomentum
        table.cell(t,10,6,stringmomentum_1_hour,text_color=color.rgb(8, 8, 8),text_size=table_text_size, bgcolor=color.new(CLS_1_hour>MA1_1_hour and CLS_1_hour>MA2_1_hour and MA1_1_hour<MA2_1_hour ? cell_phase1 : (CLS_1_hour>MA1_1_hour and CLS_1_hour>MA2_1_hour and MA1_1_hour>MA2_1_hour) ? cell_phase2 : (CLS_1_hour<MA1_1_hour and CLS_1_hour>MA2_1_hour and MA1_1_hour>MA2_1_hour) ?cell_phase3 :(CLS_1_hour<MA1_1_hour and CLS_1_hour<MA2_1_hour and MA1_1_hour>MA2_1_hour) ? cell_phase4:(CLS_1_hour<MA1_1_hour and CLS_1_hour<MA2_1_hour and MA1_1_hour<MA2_1_hour) ? cell_phase5:(CLS_1_hour>MA1_1_hour and CLS_1_hour<MA2_1_hour and MA1_1_hour<MA2_1_hour) ? cell_phase6:col_col,cell_transp))


//---------------------- 4 Hour chart ----------------------------------

    table.cell(t,1,7, "4 Hour",text_color=color.white,text_size=table_text_size, bgcolor=color.rgb(0, 68, 255))
    if  showCls
        table.cell(t,2,7, str.tostring(CLS_4_hour, '#.###'),text_color=color.new(CLS_4_hour >CLS_4_hour[2] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(CLS_4_hour >CLS_4_hour[2] ? cell_up : cell_dn ,cell_transp))
    if  showMA01
        table.cell(t,3,7, str.tostring(MA1_4_hour, '#.###'),text_color=color.new(MA1_4_hour >MA1_4_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_4_hour >MA1_4_hour[1]  ? cell_up : cell_dn ,cell_transp))
    if  showMA02
        table.cell(t,4,7, str.tostring(MA2_4_hour, '#.###'),text_color=color.new(MA2_4_hour >MA2_4_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA2_4_hour >MA2_4_hour[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACross
        table.cell(t,5,7, MA1_4_hour > MA2_4_hour ? "Bullish" : "Bearish",text_color=color.new(MA1_4_hour > MA2_4_hour ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_4_hour > MA2_4_hour ? cell_up : cell_dn ,cell_transp))
    if  showRSI
        table.cell(t,6,7, str.tostring(RSI_4_hour, '#.###'),text_color=color.new(RSI_4_hour > 50 ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(RSI_4_hour > 50 ? cell_up : cell_dn ,cell_transp))
    if  showMACDV
        table.cell(t,7,7,str.tostring(MACDV_4_hour, '#.###'),text_color=color.new(MACDV_4_hour > MACDV_4_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MACDV_4_hour > MACDV_4_hour[1] ? cell_up : cell_dn ,cell_transp))
    if  showSignalV
        table.cell(t,8,7,str.tostring(SignalV_4_hour, '#.###'),text_color=color.new(SignalV_4_hour > SignalV_4_hour[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(SignalV_4_hour> SignalV_4_hour[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACDV_Status
        table.cell(t,9,7,stringmacdv_4_hour,text_color=color.rgb(7, 7, 7),text_size=table_text_size, bgcolor=color.new(MACDV_4_hour>50 ? cell_up :MACDV_4_hour<-50 ?  cell_dn:cell_MACDV4  ,cell_transp)) 
    if  showmomentum
        table.cell(t,10,7,stringmomentum_4_hour,text_color=color.rgb(7, 7, 7),text_size=table_text_size, bgcolor=color.new(CLS_4_hour>MA1_4_hour and CLS_4_hour>MA2_4_hour and MA1_4_hour<MA2_4_hour ? cell_phase1 : (CLS_4_hour>MA1_4_hour and CLS_4_hour>MA2_4_hour and MA1_4_hour>MA2_4_hour) ? cell_phase2 : (CLS_4_hour<MA1_4_hour and CLS_4_hour>MA2_4_hour and MA1_4_hour>MA2_4_hour) ?cell_phase3 :(CLS_4_hour<MA1_4_hour and CLS_4_hour<MA2_4_hour and MA1_4_hour>MA2_4_hour) ? cell_phase4:(CLS_4_hour<MA1_4_hour and CLS_4_hour<MA2_4_hour and MA1_4_hour<MA2_4_hour) ? cell_phase5:(CLS_4_hour>MA1_4_hour and CLS_4_hour<MA2_4_hour and MA1_4_hour<MA2_4_hour) ? cell_phase6:col_col,cell_transp))


//---------------------- 1 Day chart ----------------------------------

    table.cell(t,1,9, "1 Day",text_color=color.white,text_size=table_text_size, bgcolor=color.rgb(0, 68, 253))
    if  showCls
        table.cell(t,2,9, str.tostring(CLS_1_day, '#.###'),text_color=color.new(CLS_1_day >CLS_1_day[2] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(CLS_1_day >CLS_1_day[2] ? cell_up : cell_dn ,cell_transp))
    if  showMA01
        table.cell(t,3,9, str.tostring(MA1_1_day, '#.###'),text_color=color.new(MA1_1_day >MA1_1_day[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_1_day >MA1_1_day[1]  ? cell_up : cell_dn ,cell_transp))
    if  showMA02
        table.cell(t,4,9, str.tostring(MA2_1_day, '#.###'),text_color=color.new(MA2_1_day >MA2_1_day[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA2_1_day >MA2_1_day[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACross
        table.cell(t,5,9, MA1_1_day > MA2_1_day ? "Bullish" : "Bearish",text_color=color.new(MA1_1_day > MA2_1_day ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MA1_1_day > MA2_1_day ? cell_up : cell_dn ,cell_transp))
    if  showRSI
        table.cell(t,6,9, str.tostring(RSI_1_day, '#.###'),text_color=color.new(RSI_1_day > 50 ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(RSI_1_day > 50 ? cell_up : cell_dn ,cell_transp))
    if  showMACDV
        table.cell(t,7,9,str.tostring(MACDV_1_day, '#.###'),text_color=color.new(MACDV_1_day > MACDV_1_day[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(MACDV_1_day > MACDV_1_day[1] ? cell_up : cell_dn ,cell_transp))
    if  showSignalV
        table.cell(t,8,9,str.tostring(SignalV_1_day, '#.###'),text_color=color.new(SignalV_1_day > SignalV_1_day[1] ? cell_up : cell_dn ,0),text_size=table_text_size, bgcolor=color.new(SignalV_1_day> SignalV_1_day[1] ? cell_up : cell_dn ,cell_transp))
    if  showMACDV_Status
        table.cell(t,9,9,stringmacdv_1_day,text_color=color.rgb(5, 5, 5),text_size=table_text_size, bgcolor=color.new(MACDV_1_day>50 ? cell_up :MACDV_1_day<-50 ?  cell_dn:cell_MACDV4  ,cell_transp)) 
    if  showmomentum
        table.cell(t,10,9,stringmomentum_1_day,text_color=color.rgb(7, 7, 7),text_size=table_text_size, bgcolor=color.new(CLS_1_day>MA1_1_day and CLS_1_day>MA2_1_day and MA1_1_day<MA2_1_day ? cell_phase1 : (CLS_1_day>MA1_1_day and CLS_1_day>MA2_1_day and MA1_1_day>MA2_1_day) ? cell_phase2 : (CLS_1_day<MA1_1_day and CLS_1_day>MA2_1_day and MA1_1_day>MA2_1_day) ?cell_phase3 :(CLS_1_day<MA1_1_day and CLS_1_day<MA2_1_day and MA1_1_day>MA2_1_day) ? cell_phase4:(CLS_1_day<MA1_1_day and CLS_1_day<MA2_1_day and MA1_1_day<MA2_1_day) ? cell_phase5:(CLS_1_day>MA1_1_day and CLS_1_day<MA2_1_day and MA1_1_day<MA2_1_day) ? cell_phase6:col_col,cell_transp))


//---- Display data code end ----//
//End dahs board




