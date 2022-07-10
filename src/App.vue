<script>
export default {
  data() {
    return {
      flag: false,
      x: 0,
      y: 0,
      containerItems: [],
      newLeft: 0,
      newTop: 0,
      newWidth: 0,
      newHeight: 0,
      color: "#00ff00",
    };
  },
  methods: {
    mousedown(e) {
      console.log(e.offsetX, e.offsetY);
      console.log(this.flag);
      // проверка клика по области
      // for (let i = this.containerItems.length - 1; i >= 0; i--) {
      //   if ((e.offsetX >= this.containerItems[i].left && e.offsetX <= this.containerItems[i].left + this.containerItems[i].width) && (e.offsetY >= this.containerItems[i].top && e.offsetY <= this.containerItems[i].top + this.containerItems[i].height)) {
      //     this.flag = false;
      //     console.log(this.flag);
      //   } 
      // }
      this.flag = true;
      // верхний левый угол
      this.x = e.offsetX;
      this.y = e.offsetY;
    },
    mouseup(e) {
      this.addNewRect(this.newLeft, this.newTop, this.newWidth, this.newHeight);
      this.flag = false;
    },
    mousemove(e) {
      if (this.flag) {
        this.drawRect(e);
      }
    },
    drawRect(e) {
      if (this.flag) {
        const canvas = document.getElementById("canvass");
        let ctx = canvas.getContext("2d");
        let x = this.x;
        let y = this.y;
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.beginPath();
        this.containerItems.forEach((element) => {
          ctx.strokeStyle = "#5eead4";
          ctx.strokeRect(
            element.left,
            element.top,
            element.width,
            element.height
          );
        });

        this.newWidth = e.offsetX - x;
        this.newHeight = e.offsetY - y;
        this.newLeft = x;
        this.newTop = y;
        let newRight = this.newLeft + this.newWidth;
        let newBottom = this.newTop + this.newHeight;
        ctx.strokeStyle = this.color;
        for (
          let element = this.containerItems.length - 1;
          element >= 0;
          element--
        ){
          if (
            // лев верх точка
            (this.newLeft >= this.containerItems[element].left &&
              this.newLeft <= this.containerItems[element].right &&
              this.newTop >= this.containerItems[element].top &&
              this.newTop <= this.containerItems[element].bottom) ||
            // прав верх точка
            (newRight >= this.containerItems[element].left &&
              newRight <= this.containerItems[element].right &&
              this.newTop >= this.containerItems[element].top &&
              this.newTop <= this.containerItems[element].bottom) ||
            // лев ниж точка
            (this.newLeft >= this.containerItems[element].left &&
              this.newLeft <= this.containerItems[element].right &&
              newBottom >= this.containerItems[element].top &&
              newBottom <= this.containerItems[element].bottom) ||
            // прав верх точка
            (newRight >= this.containerItems[element].left &&
              newRight <= this.containerItems[element].right &&
              newBottom >= this.containerItems[element].top &&
              newBottom <= this.containerItems[element].bottom) ||
            // ширина больше, пересечение по верхней прямой
            (this.newLeft < this.containerItems[element].left &&
              this.newLeft < this.containerItems[element].right &&
              newRight > this.containerItems[element].left &&
              newRight > this.containerItems[element].right &&
              this.newTop >= this.containerItems[element].top &&
              this.newTop <= this.containerItems[element].bottom) ||
            // ширина больше, пересечение по нижней прямой
            (this.newLeft < this.containerItems[element].left &&
              this.newLeft < this.containerItems[element].right &&
              newRight > this.containerItems[element].left &&
              newRight > this.containerItems[element].right &&
              newBottom >= this.containerItems[element].top &&
              newBottom <= this.containerItems[element].bottom) ||
              // высота больше, пересечение по левой прямой
            (this.newTop < this.containerItems[element].top &&
              this.newTop < this.containerItems[element].bottom &&
              newBottom > this.containerItems[element].top &&
              newBottom > this.containerItems[element].bottom &&
              this.newLeft >= this.containerItems[element].left &&
              this.newLeft <= this.containerItems[element].right) ||
              // высота больше, пересечение по правой прямой
            (this.newTop < this.containerItems[element].top &&
              this.newTop < this.containerItems[element].bottom &&
              newBottom > this.containerItems[element].top &&
              newBottom > this.containerItems[element].bottom &&
              newRight >= this.containerItems[element].left &&
              newRight <= this.containerItems[element].right)
          ) {
            ctx.strokeStyle = "red";
            console.log(false);
          }
        }
        // this.containerItems.forEach((element) => {
        //             if (
        //     (this.newLeft >= element.left &&
        //       this.newLeft <= element.right &&
        //       this.newTop >= element.top &&
        //       this.newTop <= element.bottom) ||
        //     (newRight >= element.left &&
        //       newRight <= element.right &&
        //       this.newTop >= element.top &&
        //       this.newTop <= element.bottom) ||
        //     (this.newLeft >= element.left &&
        //       this.newLeft <= element.right &&
        //       newBottom >= element.top &&
        //       newBottom <= element.bottom) ||
        //     (newRight >= element.left &&
        //       newRight <= element.right &&
        //       newBottom >= element.top &&
        //       newBottom <= element.bottom)
        //   ) {
        //     ctx.strokeStyle = "red";
        //   } else {
        //     ctx.strokeStyle = this.color;
        //   }
        // });

        ctx.strokeRect(x, y, this.newWidth, this.newHeight);
      }
    },
    addNewRect(left, top, width, height) {
      this.containerItems.push({
        width: width,
        height: height,
        left: left,
        top: top,
        right: left + width,
        bottom: top + height,
        active: false,
      });
      console.log(this.containerItems);
      // console.log(this.containerItems);
      //  отрисовывать левые крайние точки у области с active true
    },
  },
};
</script>

<template>
  <div class="">
    <canvas
      id="canvass"
      width="1000"
      height="600"
      class="bg-slate-800 bg-[url('../public/bg-canvas.jpg')] mx-auto my-4"
      @mousedown="mousedown"
      @mousemove="mousemove"
      @mouseup="mouseup"
      @click="changeActive"
    >
    </canvas>
  </div>
</template>
