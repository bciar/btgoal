<template>
    <div ref:"calendar" id="calendar"></div>
</template>

<script>
    require('fullcalendar')
    export default {
        props: {
            events: {
                default() {
                    return []
                },
            },

            eventSources: {
                default() {
                    return []
                },
            },

            editable: {
                default() {
                    return true
                },
            },

            selectable: {
                default() {
                    return true
                },
            },

            selectHelper: {
                default() {
                    return true
                },
            },

            header: {
                default() {
                    return {
                        left:   'prev,next today',
                        center: 'title',
                        right:  'month,agendaWeek,agendaDay'
                    }
                },
            },

            defaultView: {
                default() {
                    return 'agendaWeek'
                },
            },

            sync: {
                default() {
                    return false
                }
            },
        },

        mounted() {
            const cal = $(this.$el),
                self = this

            cal.fullCalendar({
                header: this.header,
                defaultView: this.defaultView,
                editable: this.editable,
                selectable: this.selectable,
                selectHelper: this.selectHelper,
                aspectRatio: 2,
                timeFormat: 'HH:mm',
                scrollTime: "6:00:00",
                events: self.events,
                eventSources: self.eventSources,
                firstDay: 1,
                height: 1050,
                views: {
                    week: { // name of view
                        titleFormat: 'D MMM YYYY',
                        columnFormat: 'ddd D/M'
                        // other view-specific options here
                    },
                    day: {
                        titleFormat: 'D MMM YYYY'
                    }
                },
                eventRender(event, element) {
                    if (this.sync) {
                        self.events = cal.fullCalendar('clientEvents')
                    }
                },

                eventDestroy(event) {
                    if (this.sync) {
                        self.events = cal.fullCalendar('clientEvents')
                    }
                },

                eventClick(event) {
                    self.$emit('event-selected', event)

                },

                eventDrop(event) {
                    self.$emit('event-drop', event)
                },

                eventResize(event) {
                    self.$emit('event-resize', event)
                },

                select(start, end, jsEvent) {
                    self.$emit('event-created', {
                        start,
                        end,
                        allDay: !start.hasTime() && !end.hasTime(),
                    })
                },
            })
        },

        watch: {
            events: {
                deep: true,
                handler(val) {
                    $(this.$el).fullCalendar('rerenderEvents')
                },
            }
        },

        events: {
            'remove-event'(event) {
                $(this.$el).fullCalendar('removeEvents', event.id)
            },
            'rerender-events'(event) {
                $(this.$el).fullCalendar('rerenderEvents')
            },
            'refetch-events'(event) {
                $(this.$el).fullCalendar('refetchEvents')
            },
            'render-event'(event) {
                $(this.$el).fullCalendar('renderEvent', event)
            },
            'reload-events'() {
                $(this.$el).fullCalendar('removeEvents')
                $(this.$el).fullCalendar('addEventSource', this.events)
            },
            'rebuild-sources'() {
                $(this.$el).fullCalendar('removeEvents')
                this.eventSources.map(event => {
                    $(this.$el).fullCalendar('addEventSource', event)
                })
            },
        },
    }
</script>

<style src="./fullcalendar.css"></style>