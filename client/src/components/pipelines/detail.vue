<template>
    <div>
    <v-container>
        <breadcrumbs :items="breadcrumbItems"></breadcrumbs>
    </v-container>
    <v-container :fluid="true">
        <h1>{{ this.pipeline }}</h1>
        <v-layout>
                <v-row>
                    <v-col v-for="phase in activePhases" :key="phase.name">
                        <p><span class="text-uppercase">{{phase.name}}</span><br /><span class="caption">[{{phase.context}}]</span></p>


                        <Appcard v-for="app in phase.apps" :key="app.name"
                            :pipeline="pipeline"
                            :phase="phase.name"
                            :app="app" />

                        <v-btn
                        elevation="2"
                        icon
                        large
                        :to="{ name: 'New App', params: { phase: phase.name }}"
                        class="mt-5 navBG"
                        >
                            <v-icon>
                                mdi-plus
                            </v-icon>
                        </v-btn>
                    </v-col>

                </v-row>
        </v-layout>
    </v-container>
    </div>
</template>

<script>
import axios from "axios";
import Appcard from "./appcard.vue";

export default {
    mounted() {
        this.loadPipeline();
    },
    props: {
      pipeline: {
        type: String,
        default: "MISSSING"
      },
    },
    data () {return {
        breadcrumbItems: [
            {
                text: 'DASHBOARD',
                disabled: false,
                href: '#/',
            },
            {
                text: 'PIPELINE:'+this.pipeline,
                disabled: true,
                href: '#/pipeline/'+this.pipeline+'/apps',
            }
        ],
        reviewapps: false,
        phases: false,
    }},
    computed: {
        activePhases() {
            let phases = [];
            if (this.phases) {
                this.phases.forEach(phase => {
                    if (phase.enabled) {
                        phases.push(phase);
                    }
                });
            }
            return phases;
        }
    },
    components: {
        Appcard,
        breadcrumbs: () => import('../breadcrumbs.vue'),
    },
    methods: {
      async loadPipeline() {
        const self = this;
        axios.get('/api/pipelines/' + this.pipeline + '/apps')
        .then(response => {
            self.phases = response.data.phases;
            self.reviewapps = response.data.reviewapps;
            return response.data.phases;
        })
        .catch(error => {
            console.log(error);
        });
      },
    },
}
</script>