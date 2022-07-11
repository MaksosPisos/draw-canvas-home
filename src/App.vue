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
      newRight: 0,
      newBottom: 0,
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
      this.addNewRect(this.newLeft, this.newTop, this.newWidth, this.newHeight, true);
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
        let circle = canvas.getContext("2d");
        let x = this.x;
        let y = this.y;
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        ctx.beginPath();
        this.containerItems.forEach((element) => {
          console.log('i');
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
        this.newRight = this.newLeft + this.newWidth;
        this.newBottom = this.newTop + this.newHeight;

        ctx.arc(x, y, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.newRight, y)
        ctx.arc(this.newRight, y, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(x, this.newBottom)
        ctx.arc(x, this.newBottom, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.newRight, this.newBottom)
        ctx.arc(this.newRight, this.newBottom, 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;

        for (
          let element = this.containerItems.length - 1;
          element >= 0;
          element--
        ) {
          if (
            // лев верх точка
            (this.newLeft >= this.containerItems[element].left &&
              this.newLeft <= this.containerItems[element].right &&
              this.newTop >= this.containerItems[element].top &&
              this.newTop <= this.containerItems[element].bottom) ||
            // прав верх точка
            (this.newRight >= this.containerItems[element].left &&
              this.newRight <= this.containerItems[element].right &&
              this.newTop >= this.containerItems[element].top &&
              this.newTop <= this.containerItems[element].bottom) ||
            // лев ниж точка
            (this.newLeft >= this.containerItems[element].left &&
              this.newLeft <= this.containerItems[element].right &&
              this.newBottom >= this.containerItems[element].top &&
              this.newBottom <= this.containerItems[element].bottom) ||
            // прав верх точка
            (this.newRight >= this.containerItems[element].left &&
              this.newRight <= this.containerItems[element].right &&
              this.newBottom >= this.containerItems[element].top &&
              this.newBottom <= this.containerItems[element].bottom) ||
            // ширина больше, пересечение по верхней прямой
            (this.newLeft < this.containerItems[element].left &&
              this.newLeft < this.containerItems[element].right &&
              this.newRight > this.containerItems[element].left &&
              this.newRight > this.containerItems[element].right &&
              this.newTop >= this.containerItems[element].top &&
              this.newTop <= this.containerItems[element].bottom) ||
            // ширина больше, пересечение по нижней прямой
            (this.newLeft < this.containerItems[element].left &&
              this.newLeft < this.containerItems[element].right &&
              this.newRight > this.containerItems[element].left &&
              this.newRight > this.containerItems[element].right &&
              this.newBottom >= this.containerItems[element].top &&
              this.newBottom <= this.containerItems[element].bottom) ||
            // высота больше, пересечение по левой прямой
            (this.newTop < this.containerItems[element].top &&
              this.newTop < this.containerItems[element].bottom &&
              this.newBottom > this.containerItems[element].top &&
              this.newBottom > this.containerItems[element].bottom &&
              this.newLeft >= this.containerItems[element].left &&
              this.newLeft <= this.containerItems[element].right) ||
            // высота больше, пересечение по правой прямой
            (this.newTop < this.containerItems[element].top &&
              this.newTop < this.containerItems[element].bottom &&
              this.newBottom > this.containerItems[element].top &&
              this.newBottom > this.containerItems[element].bottom &&
              this.newRight >= this.containerItems[element].left &&
              this.newRight <= this.containerItems[element].right)
          ) {
            ctx.strokeStyle = "red";
            ctx.fillStyle = "red";
          }
        }
        ctx.fill();
        ctx.strokeRect(x, y, this.newWidth, this.newHeight);

      }
    },
    addNewRect(left, top, width, height, active) {
      this.containerItems.forEach(element => {
        element.active = false
      })
      this.containerItems.push({
        width: width,
        height: height,
        left: left,
        top: top,
        right: left + width,
        bottom: top + height,
        active: active,
      });
      console.log(this.containerItems);
      //  отрисовывать крайние точки у области с active true
    },
    changeActive(e) {

    },
  },
};
</script>

<template>
  <div class="">
    <canvas id="canvass" width="1000" height="600" class="bg-slate-800 bg-[url('../public/bg-canvas.jpg')] mx-auto my-4"
      @mousedown="mousedown" @mousemove="mousemove" @mouseup="mouseup" @click="changeActive">
    </canvas>
  </div>
</template>
<!-- при клике по области менять active, и перерисовывать цвет.... -->