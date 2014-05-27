Time-Line-Indicator
===================

Plugin that adds a time indicator in the form of a line to Extensible's Calendar Pro to be shown on the Day/Week/Multi-Day Views.

Here is the code:

**calendar/template/DateBody.js** -- add the following line before the '<table ...>' in the superclass call:
`
'<hr id="' + this.id.substring(0, this.id.length-3) + '-hd-time-indicator" class="time-indicator" style="top:0px;visibility: hidden;"></hr>',
`

So it should look something like this:
`
calendar.template.DayBody.superclass.constructor.call(this,
            '<hr id="' + this.id.substring(0, this.id.length-3) + '-hd-time-indicator" class="time-indicator" style="top:0px;visibility: hidden;"></hr>',
            '<table class="ext-cal-bg-tbl" cellspacing="0" cellpadding="0" style="height:{dayHeight}px;">',
`

next step:
----------
**calendar/view/Month.js** -- add the following code into the initClock function's 'run' function:
`
var formattedTime = Ext.Date.format(t, Calendar.Date.use24HourTime ? 'G:i' : 'g:ia'),
                        indicator = Ext.get(this.id + '-time-indicator');

                    if(el !== null && indicator !== null){
                        // move indicator based on time
                        var hourInterval = 42,
                            minuteInterval = hourInterval / 59,
                            fullTime = formattedTime,
                            length = fullTime.length,
                            am = fullTime.substring(length-2, length) === "am",
                            h_px = am ? 0 : hourInterval * 12,
                            m_px = 0;

                        var hour = parseInt(fullTime.substring(0, length == 7 ? 2 : 1), 10),
                            min  = parseInt(fullTime.substring(length == 7 ? 3 : 2, length == 7 ? 5 : 4), 10);

                        h_px = h_px + ((!am && hour == 12) ? 0 : (hourInterval * hour)),
                            m_px = m_px + (minuteInterval * min);

                        if(indicator.dom.style.visibility === 'hidden'){
                            indicator.show();
                        }
                        indicator.setTop(h_px + m_px);
                    }
                    else if(indicator !== null){
                        indicator.hide();
                    }
`

So it should look like this:
`
//private
    initClock : function(){
        if(Ext.fly(me.itemId + '-clock') !== null){
            me.prevClockDay = new Date().getDay();
            if(me.clockTask){
                Ext.util.TaskManager.stop(me.clockTask);
            }
            me.clockTask = Ext.util.TaskManager.start({
                //gets called every second
                run: function(){
                    var el = Ext.fly(this.id + '-clock'),
                        t = new Date();

                    var formattedTime = Ext.Date.format(t, Calendar.Date.use24HourTime ? 'G:i' : 'g:ia'),
                        indicator = Ext.get(this.id + '-time-indicator');

                    if(el !== null && indicator !== null){
                        // move indicator based on time
                        var hourInterval = 42,
                            minuteInterval = hourInterval / 59,
                            fullTime = formattedTime,
                            length = fullTime.length,
                            am = fullTime.substring(length-2, length) === "am",
                            h_px = am ? 0 : hourInterval * 12,
                            m_px = 0;

                        var hour = parseInt(fullTime.substring(0, length == 7 ? 2 : 1), 10),
                            min  = parseInt(fullTime.substring(length == 7 ? 3 : 2, length == 7 ? 5 : 4), 10);

                        h_px = h_px + ((!am && hour == 12) ? 0 : (hourInterval * hour)),
                            m_px = m_px + (minuteInterval * min);

                        if(indicator.dom.style.visibility === 'hidden'){
                            indicator.show();
                        }
                        indicator.setTop(h_px + m_px);
                    }
                    else if(indicator !== null){
                        indicator.hide();
                    }

                    if(t.getDay() == me.prevClockDay){
                        if(el){
                            ...
`

CSS
---
Here is the css for the time-indcator:
`
.time-indicator {
    position: relative;
    border: none;
    border-top: 1px solid red;
    border-bottom: 1px solid red;
    width: 100%;
    margin: 0;
    padding: 0;
    z-index: 1;
}
`

Optional
--------

If one wants, the event css class can be changed to have a z-index higher than the z-index in the time-indicator class:
`
.ext-cal-day-col .ext-cal-evr,
.ext-cal-day-col .ext-cal-evi {
    white-space: normal;
    border-right: 1px solid #fff;
    z-index: 2;  //time-indicator has z-index: 1;
}
`
