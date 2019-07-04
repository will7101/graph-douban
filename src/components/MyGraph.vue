<template>
  <div>
    <d3-network
      :net-nodes="nodes"
      :net-links="links"
      :options="options"
      @node-click="selectNode"
    >
    </d3-network>
  </div>
</template>

<script>
  import D3Network from "vue-d3-network/src/vue-d3-network";
  import graph from "@/assets/graph";

  export default {
    name: "MyGraph",
    components: {D3Network},
    props: {
      highlights: Object
    },
    data() {
      return {
        nodeSize: 20,
      }
    },
    computed: {
      options() {
        return {
          force: 300,
          size: {w: 800, h: 800},
          nodeSize: this.nodeSize,
          nodeLabels: false,
          linkLabels: false,
          canvas: false
        }
      },
      links() {
        graph.links.forEach((v) => {
          if (this.highlights.links.includes(v.id)) {
            v._svgAttrs = {
              'stroke-width': 2,
            };
            v._color = 'rgba(0,0,0,0.5)';
          } else {
            v._svgAttrs = {
              'stroke-width': 1,
            };
            v._color = undefined;
          }
        });
        return graph.links;
      },
      nodes() {
        graph.nodes.forEach((v) => {
          if(this.highlights.nodes.includes(v.id)) {
            v._svgAttrs = {
              'stroke-width': 3
            }
          } else {
            v._svgAttrs = {
              'stroke-width': 1
            };
          }
        });
        return graph.nodes;
      }
    },
    methods: {
      selectNode(event, node) {
        this.$emit('select-node', node)
      }
    }
  }
</script>

<style>
  .node {
    stroke-width: 1;
    stroke: rgba(0, 0, 0, 0.2);
  }

  .node:hover {
    stroke-width: 3;
    stroke: rgba(0, 0, 0, 0.4);
  }

  .link {
    stroke: rgba(50, 50, 50, 0.1)
  }

  .link:hover {
    stroke: rgba(50, 50, 50, 0.1)
  }
</style>