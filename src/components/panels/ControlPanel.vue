<style>
    .btnHomeAxis {
        width: 36px;
        min-width: 36px !important;
    }

    .btnMinWidthAuto {
        min-width: auto !important;
    }
</style>

<template>
    <v-card>
        <v-container class="pt-0">
            <v-row no-gutters class="" v-if="['standby', 'paused', 'complete', 'error'].includes(printer_state)">
                <v-col class="col-12 mt-3 pb-0 text-center">
                    <v-btn small @click="doHome" :loading="loadings.includes('homeAll')" :color="homedAxes.includes('xyz') ? 'primary' : 'warning'"><v-icon class="mr-1">mdi-home</v-icon><span class="d-none d-sm-inline">{{ $t("Dashboard.Home")}} </span>{{ $t("Dashboard.All")}}</v-btn>
                    <v-btn small @click="doQGL" :loading="loadings.includes('qgl')" color="primary" class="ml-2" v-if="'quad_gantry_level' in config">{{ $t("Dashboard.QGL")}}</v-btn>
                    <v-btn small @click="doZtilt" :loading="loadings.includes('zTilt')" color="primary" class="ml-2" v-if="'z_tilt' in config">{{ $t("Dashboard.ZTilt")}}</v-btn>
                </v-col>
            </v-row>
            <v-row no-gutters class="mt-3" v-if="['standby', 'paused', 'complete', 'error'].includes(printer_state)">
                <v-col class="text-center">
                    <v-btn-toggle dense no-gutters class="row no-gutters  mx-auto" style="flex-wrap: nowrap;" >
                        <v-btn @click="doSendMove('X-'+steps, feedrateXY)" class="btnMinWidthAuto col" v-for="steps of stepsXYsorted" v-bind:key="'x-'+steps"><span class="body-2">-{{ steps }}</span></v-btn>
                        <v-btn @click="doHomeX" :color="homedAxes.includes('x') ? 'primary' : 'warning'" :loading="loadings.includes('homeX')" class="font-weight-bold btnHomeAxis">X</v-btn>
                        <v-btn @click="doSendMove('X+'+steps, feedrateXY)" class="btnMinWidthAuto col" v-for="steps of stepsXYsortedReverse" v-bind:key="'x+'+steps"><span class="body-2">+{{ steps }}</span></v-btn>
                    </v-btn-toggle>
                </v-col>
            </v-row>
            <v-row no-gutters class="mt-3" v-if="['standby', 'paused', 'complete', 'error'].includes(printer_state)">
                <v-col class="text-center">
                    <v-btn-toggle dense no-gutters class="row no-gutters  mx-auto" style="flex-wrap: nowrap;" >
                        <v-btn @click="doSendMove('Y-'+steps, feedrateXY)" class="btnMinWidthAuto col" v-for="steps of stepsXYsorted" v-bind:key="'y-'+steps"><span class="body-2">-{{ steps }}</span></v-btn>
                        <v-btn @click="doHomeY" :color="homedAxes.includes('y') ? 'primary' : 'warning'" :loading="loadings.includes('homeY')" class="font-weight-bold btnHomeAxis">Y</v-btn>
                        <v-btn @click="doSendMove('Y+'+steps, feedrateXY)" class="btnMinWidthAuto col" v-for="steps of stepsXYsortedReverse" v-bind:key="'y+'+steps"><span class="body-2">+{{ steps }}</span></v-btn>
                    </v-btn-toggle>
                </v-col>
            </v-row>
            <v-row no-gutters class="mt-3" v-if="['standby', 'paused', 'complete', 'error'].includes(printer_state)">
                <v-col class="text-center">
                    <v-btn-toggle dense no-gutters class="row no-gutters mx-auto" style="flex-wrap: nowrap;" >
                        <v-btn @click="doSendMove('Z-'+steps, feedrateZ)" class="btnMinWidthAuto col" v-for="steps of stepsZsorted" v-bind:key="'z-'+steps"><span class="body-2">-{{ steps }}</span></v-btn>
                        <v-btn @click="doHomeZ" :color="homedAxes.includes('z') ? 'primary' : 'warning'" :loading="loadings.includes('homeZ')" class="font-weight-bold btnHomeAxis">Z</v-btn>
                        <v-btn @click="doSendMove('Z+'+steps, feedrateZ)" class="btnMinWidthAuto col" v-for="steps of stepsZsortedReverse" v-bind:key="'z+'+steps"><span class="body-2">+{{ steps }}</span></v-btn>
                    </v-btn-toggle>
                </v-col>
            </v-row>
            <v-row no-gutters v-if="this['printer/getMacros'].length > 0">
                <v-col class="text-center">
                    <v-btn v-for="(macro, index) in this['printer/getMacros']" v-bind:key="index+99" small color="primary" class="mx-1 mt-3" :loading="loadings.includes('macro_'+macro.name)" @click="doSendMacro(macro.name)">{{ macro.name.replace(/_/g, " ") }}</v-btn>
                </v-col>
            </v-row>
        </v-container>
    </v-card>
</template>

<script>
    import { mapState, mapGetters } from 'vuex'
    import Vue from "vue";

    export default {
        components: {

        },
        data: function() {
            return {

            }
        },
        computed: {
            ...mapState({
                homedAxes: state => state.printer.toolhead.homed_axes,
                config: state => state.printer.configfile.config,
                loadings: state => state.socket.loadings,
                printer_state: state => state.printer.print_stats.state,

                feedrateXY: state => state.gui.dashboard.control.feedrateXY,
                stepsXY: state => state.gui.dashboard.control.stepsXY,
                feedrateZ: state => state.gui.dashboard.control.feedrateZ,
                stepsZ: state => state.gui.dashboard.control.stepsZ,
            }),
            ...mapGetters([
                'printer/getMacros',
            ]),
            stepsXYsorted: {
                get() {
                    return [...this.$store.state.gui.dashboard.control.stepsXY].sort(function(a, b) { return b-a })
                }
            },
            stepsXYsortedReverse: {
                get() {
                    return [...this.$store.state.gui.dashboard.control.stepsXY].sort(function(a, b) { return a-b })
                }
            },
            stepsZsorted: {
                get() {
                    return [...this.$store.state.gui.dashboard.control.stepsZ].sort(function(a, b) { return b-a })
                }
            },
            stepsZsortedReverse: {
                get() {
                    return [...this.$store.state.gui.dashboard.control.stepsZ].sort(function(a, b) { return a-b })
                }
            }
        },
        methods: {
            doHome() {
                this.$store.commit('server/addEvent', { message: "G28", type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'homeAll' });
                this.$socket.sendObj('printer.gcode.script', { script: "G28" }, "socket/removeLoading", { name: 'homeAll' });
            },
            doHomeX() {
                this.$store.commit('server/addEvent', { message: "G28 X", type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'homeX' });
                this.$socket.sendObj('printer.gcode.script', { script: "G28 X" }, "socket/removeLoading", { name: 'homeX' });
            },
            doHomeY() {
                this.$store.commit('server/addEvent', { message: "G28 Y", type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'homeY' });
                this.$socket.sendObj('printer.gcode.script', { script: "G28 Y" }, "socket/removeLoading", { name: 'homeY' });
            },
            doHomeZ() {
                this.$store.commit('server/addEvent', { message: "G28 Z", type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'homeZ' });
                this.$socket.sendObj('printer.gcode.script', { script: "G28 Z" }, "socket/removeLoading", { name: 'homeZ' });
            },
            doQGL() {
                this.$store.commit('server/addEvent', { message: "QUAD_GANTRY_LEVEL", type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'qgl' });
                this.$socket.sendObj('printer.gcode.script', { script: "QUAD_GANTRY_LEVEL" }, "socket/removeLoading", { name: 'qgl' });
            },
            doZtilt() {
                this.$store.commit('server/addEvent', { message: "Z_TILT_ADJUST", type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'zTilt' });
                this.$socket.sendObj('printer.gcode.script', { script: "Z_TILT_ADJUST" }, "socket/removeLoading", { name: 'zTilt' });
            },
            doSendMove(gcode, feedrate) {
                gcode = "G91" + "\n" +
                    "G1 " + gcode + " F"+feedrate*60 + "\n" +
                    "G90";

                this.doSend(gcode);
            },
            doSend(gcode) {
                this.$store.commit('server/addEvent', { message: gcode, type: 'command' });
                Vue.prototype.$socket.sendObj('printer.gcode.script', { script: gcode }, "server/getGcodeRespond");
            },
            doSendMacro(gcode) {
                this.$store.commit('server/addEvent', { message: gcode, type: 'command' });
                this.$store.commit('socket/addLoading', { name: 'macro_'+gcode });
                Vue.prototype.$socket.sendObj('printer.gcode.script', { script: gcode }, "socket/removeLoading", { name: 'macro_'+gcode });
            },
        },
    }
</script>