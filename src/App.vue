<template>
  <div id="app">
    <my-header></my-header>
    <b-container fluid>
      <b-row>
        <b-col cols="4">
          <control-panel
            @shortest-path="shortestPath"
            @spanning-tree="spanningTree"
            :message="message"
          >
          </control-panel>
        </b-col>

        <b-col cols="8" class="text-center py-2">
          <my-graph
            :highlights="highlights"
            @select-node="selectNode"
          >
          </my-graph>
          <div v-if="nodeSelected" class="text-center my-3">
            选中的结点：{{ nodeSelected.name }}，id为{{ nodeSelected.id }}
          </div>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
  import MyHeader from "@/components/MyHeader";
  import MyGraph from "@/components/MyGraph";
  import ControlPanel from "@/components/ControlPanel";

  import graph from "@/assets/graph";

  export default {
    name: 'app',
    components: {
      ControlPanel,
      MyGraph,
      MyHeader,
    },
    data() {
      return {
        nodeSelected: null,
        highlights: {
          nodes: [],
          links: [1, 2, 3]
        },
        mst: [],
        message: ""
      }
    },
    methods: {
      selectNode(node) {
        this.nodeSelected = node;
      },

      shortestPath(sid, tid) {
        let adj = {};

        function addEdge(u, e) {
          if (!adj[u]) adj[u] = [];
          adj[u].push(e);
        }

        graph.links.forEach((v) => {
          addEdge(v.sid, v);
          addEdge(v.tid, v);
        });

        let d = {}, pre = {}, preE = {};

        let unvis = [];
        graph.nodes.forEach((v) => {
          unvis.push(v.id);
          d[v.id] = Infinity;
        });
        d[sid] = 0;

        for (; ;) {
          let u = -1;
          for (let v of unvis) {
            if (u === -1 || d[v] < d[u]) u = v;
          }
          if (u === -1) break;
          unvis.splice(unvis.indexOf(u), 1);

          for (let e of adj[u]) {
            let v = e.tid + e.sid - u;
            if (d[v] > d[u] + e.weight) {
              d[v] = d[u] + e.weight;
              pre[v] = u;
              preE[v] = e;
            }
          }
        }

        if (d[tid] === Infinity) {
          this.message = "起点与终点不连通！";
          return;
        }

        this.highlights.nodes = [sid];
        this.highlights.links = [];

        for (let u = tid; u !== sid; u = pre[u]) {
          window.console.log(u, sid);
          this.highlights.nodes.push(u);
          this.highlights.links.push(preE[u].id);
        }
        this.message = `最短距离为 ${d[tid]}`;
      },

      spanningTree() {
        this.highlights.links = Array.from(this.mst);
      }
    },
    mounted() {
      let fa = {};

      function find(x) {
        return x === fa[x] ? x : (fa[x] = find(fa[x]));
      }

      graph.nodes.forEach((v) => {
        fa[v.id] = v.id;
      });
      graph.links.forEach((v, i) => {
        v.id = i;
      });
      graph.links.sort((a, b) => {
        return a.weight - b.weight
      });
      let sum = 0.0;
      graph.links.forEach((v) => {
        let x = find(v.sid), y = find(v.tid);
        if (x === y) return;
        fa[x] = y;
        this.mst.push(v.id);
        sum += v.weight;
      });
      let colorPool = ['violet', 'salmon', 'limegreen', 'orange', 'blue', 'yellow', 'red', 'aqua', 'fuchsia'];
      let colors = {};
      graph.nodes.forEach((v) => {
        let x = find(v.id);
        if (!colors[x]) {
          colors[x] = colorPool.shift();
        }
        v._color = colors[x];
      });
      this.message = `生成树总长度为 ${sum}`;
    }
  }
</script>

<style>

</style>
