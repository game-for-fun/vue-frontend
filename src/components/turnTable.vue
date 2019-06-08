<template>
  <div id="turn-table">

    <div :class="winnerClassHandler" :style="winnerStyleHandler(winner.color)" :data-winner="winner.label">
    </div>
    <div v-if="isOpen" class="setting">
      <div class="bgc" @click="closeSetting"></div>
      <div class="list">
        <div class="item">
          <div class="cell">選項</div>
          <div class="cell">色票</div>
        </div>
        <div class="item" v-for="(item, index) in items" :key="item.label">
          <div class="cell">
            <input type="text" :value="item.label" @change="changeItemLabel($event, index)">
          </div>
          <div class="cell" :style="cellStyleHandler(item.color,index)">
            <input type="text" :value="item.color" @change="changeItemColor($event, index)">
          </div>
        </div>
      </div>
    </div>
    <div class="go" @click="rotateTurnTable">
      <span>GO</span>
    </div>
    <div class="container" :style="rotateHandler">

      <div class="item" v-for="(item,index) in list" :key="item.label" :style="itemStyleHandler(item,index)"><img class="skew-back" :src="require(`../assets/roleSmall/${item.urlPart}-small.jpg`)" alt="">
        <span class="skew-back">{{item.label}}</span>
      </div>

    </div>
    <div class="control">
      <!-- <div class="button" @click="openSetting">轉盤設定</div> -->
    </div>
    <!--抽卡弹窗st-->
    <Dialog :visible.sync="show"></Dialog>
    <!--ed-->

  </div>

</template>

<script>
import Dialog from "@/components/turnTable/dialog";
const CIRCLE_DEGREE = 360;
const TARGET_DEGREE_START = 18;
const TARGET_DEGREE_END = 90;
const DEUCE = {
  label: "平手",
  color: "F8CDDE"
};
const COLORS = ["305F72", "F1D1B5", "F0B7A4", "F18C8E", "C8E6F5", "5CA0D3"];
export default {
  name: "turnTable",
  props: {
    msg: String
  },
  components: {
    Dialog
  },
  data() {
    return {
      show: false,
      isFinish: false,
      isOpen: false,
      rotateCount: -72,
      items: [
        {
          label: "孙悟饭",
          color: "E0BECA",
          urlPart: "sunwufan"
        },
        {
          label: "沙鲁",
          color: "A7DBE2",
          urlPart: "shalu"
        },

        {
          label: "拉蒂兹",
          color: "ddd",
          urlPart: "ladizi"
        },
        {
          label: "贝吉塔",
          color: "af3d3d",
          urlPart: "beijita"
        },
        {
          label: "天津饭",
          color: "3d60af",
          urlPart: "tianjinfan"
        }
      ],
      DEUCE,
      COLORS,
      CIRCLE_DEGREE,
      TARGET_DEGREE_START,
      TARGET_DEGREE_END
    };
  },
  computed: {
    rotateHandler() {
      return {
        transform: `rotate(${this.rotateCount}deg)`
      };
    },
    list() {
      const deg = this.CIRCLE_DEGREE / this.items.length;
      const labels = this.items
        .slice()
        .map(i => i.label)
        .reverse();
      return this.items.slice().map(({ label, color, urlPart }, index) => {
        return {
          startDeg: deg * index,
          endDeg: deg * (index + 1),
          label: label,
          color,
          urlPart
        };
      });
    },
    winner() {
      if (this.rotateCount === 0) return "";
      const result = [];
      this.list.forEach(item => {
        if (
          item.startDeg >= this.TARGET_DEGREE_START &&
          item.startDeg <= this.TARGET_DEGREE_END
        ) {
          result.push(item);
        }
      });
      return result.length === 1 ? result[0] : this.DEUCE;
    },
    winnerClassHandler() {
      return {
        winner: true,
        hide: this.isFinish
      };
    }
  },
  methods: {
    itemStyleHandler(item, index) {
      return {
        backgroundColor: `#${item.color}`,
        transform: `rotate(${360 / this.items.length +
          index * (360 / this.items.length)}deg) skewY(-${360 /
          (this.items.length * 4)}deg)`
      };
    },
    winnerStyleHandler(color) {
      return {
        backgroundColor: `#${color}`
      };
    },
    cellStyleHandler(color) {
      return {
        backgroundColor: `#${color}`
      };
    },
    rotateTurnTable() {
      if (this.isFinish) return false;
      this.isFinish = true;
      const random =
        Math.floor(Math.random() * this.CIRCLE_DEGREE) + this.CIRCLE_DEGREE * 5;
      this.rotateCount = this.rotateCount + random;
      this.list.forEach(item => {
        item.startDeg = (item.startDeg + random) % 360;
        item.endDeg = (item.endDeg + random) % 360;
      });
      setTimeout(() => {
        this.isFinish = false;
      }, 2000);
    },
    openSetting() {
      this.isOpen = true;
    },
    closeSetting() {
      this.isOpen = false;
    },
    changeItemLabel($event, index) {
      this.items[index].label = $event.target.value;
    },
    changeItemColor($event, index) {
      const value = $event.target.value;
      const regex = /^[0-9a-fA-F]{6}$|^[0-9a-fA-F]{3}$/;
      this.items[index].color = regex.test(value)
        ? value
        : this.COLORS[Math.floor(Math.random() * this.COLORS.length)];
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style  lang="scss">
$color: #e94e3d;
$box-shadow-width: 10px;
$negative-box-shadow-width: -10px;
@mixin size($w, $h: $w) {
  width: $w;
  height: $h;
}
.skew-back {
  transform: skewY(18deg);
}
$color_red: #ef8888;
$color_black: #000;
$color_white: #fff;
$color_shadow: rgba(0, 0, 0, 0.5);

#turn-table {
  //background: url('../')
  background: url(../assets/bg_circle.jpg) top center;
  background-size: cover;
  @include size(100vw, 100vh);
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  > .setting {
    @include size(100%);
    position: absolute;
    display: flex;
    justify-content: center;
    align-items: center;
    > .bgc {
      @include size(100vw, 100vh);
      position: fixed;
      left: 0;
      top: 0;
      background-color: $color_black;
      opacity: 0.2;
      cursor: pointer;
      z-index: 100;
    }
    > .list {
      @include size(80%, auto);
      position: relative;
      display: flex;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      padding: 5%;
      background-color: $color_white;
      border-radius: 4px;
      z-index: 101;
      box-sizing: border-box;
      > .item {
        @include size(80%, 40px);
        display: flex;
        justify-content: center;
        align-items: center;
        margin-bottom: 8px;
        > .cell {
          @include size(100px, auto);
          position: relative;
          text-align: center;
          padding: 8px;
          margin-right: 16px;
          &:last-child {
            margin-right: 0;
            text-align: right;
            color: $color_white;
            &::before {
              content: "#";
              position: absolute;
              left: 10px;
              top: 50%;
              transform: translatey(-50%);
            }
          }
          > input {
            @include size(calc(100% - 20px));
            text-align: center;
            border: none;
            box-shadow: 0 1px 2px $color_shadow;
            outline: none;
          }
        }
        &:first-child {
          > .cell {
            text-align: center;
            &::before {
              display: none;
            }
            &:last-child {
              color: $color_black;
            }
          }
        }
      }
    }
  }
  > .winner {
    @include size(100%, 60px);
    position: absolute;
    left: 0;
    top: 0;
    transition: 0.5s;
    transition-delay: 2s;
    &.hide {
      opacity: 0;
      &::before,
      &::after {
        font-size: 0;
      }
    }
    &::before,
    &::after {
      @include size(auto);
      position: absolute;
      left: 50%;
      top: 50%;
      transform: translatex(-50%) translatey(-50%);
      color: $color_white;
      font-size: 16px;
      font-weight: 700;
      line-height: 60px;
    }
    &::before {
      content: "Winner：";
      margin-left: -34px;
    }
    &::after {
      content: attr(data-winner);
      margin-left: calc(34px * 0.6);
    }
  }
  > .go {
    @include size(60px);
    z-index: 10000;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translatex(-50%) translatey(-50%);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 18px;
    font-weight: 700;
    color: $color_white;
    background-color: $color_red;
    border: 10px solid $color_white;
    border-radius: 50%;
    box-sizing: border-box;
    letter-spacing: 1px;
    cursor: pointer;
    // z-index: 10;
    &::after {
      content: "";
      @include size(0px);
      position: absolute;
      right: -50%;
      top: 50%;
      transform: translatex(-45%) translatey(-50%);
      border-top: 15px solid transparent;
      border-bottom: 12px solid transparent;
      border-left: 20px solid $color_red;
      z-index: -1;
    }
  }
  > .container {
    // 加了这个属性就能fix手机端 skew的方块出圈的bug
    position: relative;
    z-index: 9999;
    @include size(270px);
    background-color: $color_white;
    border-radius: 50%;
    border: 10px solid $color_white;
    box-shadow: 0 2px 10px $color_shadow;
    transition: 2s;
    box-sizing: border-box;
    overflow: hidden;
    box-shadow: 0 0 0 12px #e94e3d;
    > .item {
      @include size(100%, 50%);
      display: flex;
      justify-content: center;
      align-items: center;
      color: $color_white;
      font-size: 36px;
      font-weight: 700;
      letter-spacing: 0.4px;
      overflow: hidden;
      position: absolute;
      top: 0;
      right: 0;
      width: 50%;
      height: 50%;
      transform-origin: 0% 100%;
      span {
        font-size: 16px;
        padding-top: 45px;
        padding-right: 45px;
        padding-left: 10px;
      }
      img {
        margin-left: 9px;
        border-radius: 8px;
      }
    }
  }
  > .control {
    @include size(auto);
    position: absolute;
    right: 36px;
    bottom: 36px;
    > .button {
      @include size(96px, 36px);
      display: flex;
      justify-content: center;
      align-items: center;
      border: none;
      border-radius: 4px;
      box-shadow: 0 5px 10px $color_shadow;
      letter-spacing: 0.2px;
      transition: 0.5s;
      cursor: pointer;
      &:hover {
        color: $color_white;
        background-color: $color_red;
        transform: translateY(-10%);
      }
    }
  }
}
</style>
