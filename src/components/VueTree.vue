<template>
  <div class="tree-box">
    <svg width="100%" height="100%">
      <g class="line" v-for="line in lines" :key="line">
        <path :d="line" :style="{fill: 'transparent', stroke: '#999'}" />
      </g>
      <g class="node" v-for="(node,index) in nodes" :key="index">
        <foreignObject :x="node.x" :y="node.y" class="foreign-bject">
          <div class="item-wrap">
            <div class="item">
              <div class="item-label">{{node.level}}级</div>
              <div class="item-value">{{node.name}}</div>
            </div>
            <div class="action">
              <span class="icon" v-if="node.parentId !=0" @click="remove(node)">x</span>
              <span class="icon" @click="add(node)">+</span>
            </div>
          </div>
        </foreignObject>
      </g>
    </svg>
  </div>
</template>

<script>
/* eslint-disable no-debugger */
export default {
  name: "",
  data() {
    return {
      list: [
        //假设初始数据
        { id: "1", parentId: "0", name: "省市" },
        { id: "1-1", parentId: "1", name: "北京市" },
        { id: "1-1-1", parentId: "1-1", name: "海淀区" },
        { id: "1-2", parentId: "1", name: "上海市" },
        { id: "1-2-1", parentId: "1-2", name: "黄浦区" },
        { id: "1-3", parentId: "1", name: "广州市" },
        { id: "1-3-1", parentId: "1-3", name: "白云区" },
        { id: "1-3-2", parentId: "1-3", name: "番禺区" }
      ],
      lines: [],
      nodes: []
    };
  },
  mounted() {
    const treeData = this.toTree(this.list);
    this.nodes = this.traverseTree(treeData[0]);
    this.lines = this.getLine(this.nodes);
  },
  methods: {
    toTree(list, parentId = "0", level = 1) {
      //平铺数据改成树形结构
      const treeData = [];
      for (let i = 0; i < list.length; i++) {
        let node = list[i];
        if (node.parentId === parentId) {
          node.level = level;
          node.x = 30 * (node.level - 1); // x 轴偏移位置
          const children = this.toTree(list, node.id, level + 1);
          if (children.length) {
            node.children = children;
          }
          treeData.push(node);
        }
      }
      return treeData;
    },
    traverseTree(node, newList = []) {
      if (!node) {
        return newList;
      }
      node.y = 60 * newList.length;
      // eslint-disable-next-line no-unused-vars
      const { children, ...rest } = node;
      newList.push({ ...rest });
      if (node.children && node.children.length > 0) {
        var i = 0;
        for (i = 0; i < node.children.length; i++) {
          this.traverseTree(node.children[i], newList);
        }
      }
      return newList;
    },
    getLine(list) {
      const items = {};
      const map = {};
      for (let i = 0; i < list.length; i++) {
        let key = list[i].parentId;
        map[list[i].id] = i;
        if (items[key]) {
          items[key].push(list[i]);
        } else {
          items[key] = [];
          items[key].push(list[i]);
        }
      }
      const lines = [];
      Object.keys(items).forEach(itemKey => {
        if (typeof map[itemKey] !== "undefined") {
          const parentNode = list[map[itemKey]];

          for (let i = 0; i < items[itemKey].length; i++) {
            const children = items[itemKey][i];
            lines.push(
              `M${parentNode.x + 10} ${parentNode.y + 36} L${parentNode.x +
                10} ${children.y + 18} L${children.x + 80} ${children.y + 18}`
            );
          }
        }
      });
      return lines;
    },
    add(node) {
      const parentId = node.id;
      let addId = new Date()
        .getTime()
        .toString()
        .slice(-3, -1);
      this.list.push({
        id: `${parentId}-${addId}`,
        name: `子节点:${addId}`,
        parentId
      });
      const treeData = this.toTree(this.list);
      this.nodes = this.traverseTree(treeData[0]);
      this.lines = this.getLine(this.nodes);
    },
    remove(node) {
      if (node.parentId === "0") return;

      let deleteIndex = -1;
      let parentIndex = -1;
      this.list.forEach((item, index) => {
        if (item.id === node.id) {
          deleteIndex = index;
        }
        if (item.id === node.parentId) {
          parentIndex = index;
        }
      });
      this.list.splice(deleteIndex, 1);
      this.list[parentIndex].children = [];

      const treeData = this.toTree(this.list);
      this.nodes = this.traverseTree(treeData[0]);
      this.lines = this.getLine(this.nodes);
    }
  }
};
</script>
<style lang="less" scoped>
.tree-box {
  height: 1000px;
}
.foreign-bject {
  width: 300px;
  height: 36px;
}
.item-wrap {
  display: flex;
  align-self: center;
}
.item {
  display: flex;
  flex-direction: row;
  border: 1px solid #eee;
  margin-right: 15px;
  height: 34px;
  border-radius: 5px;
  .item-label {
    display: flex;
    justify-content: center;
    align-items: center;
    background: #e8e8e8;
    width: 80px;
    border-radius: 5px 0 0 5px;
  }
  .item-value {
    display: flex;
    justify-content: center;
    align-items: center;
    flex: 1;
    padding: 0 16px;
    overflow-x: auto;
    max-width: 100px;
  }
}
.action {
  display: flex;
  align-self: center;
  .icon {
    display: inline-block;
    width: 26px;
    height: 26px;
    margin-right: 5px;
    line-height: 26px;
    border-radius: 50%;
    border: 1px solid #eee;
    cursor: pointer;
    &:hover {
      background: #ddd;
    }
  }
}
</style>
