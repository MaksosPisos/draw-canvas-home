<script>
export default {
  data() {
    return {
      flag: 0,
      x: 0,
      y: 0,
      containerItems: [
        {
          // active: false
        }
      ],
      newLeft: 0,
      newTop: 0,
      newWidth: 0,
      newHeight: 0,
      newRight: 0,
      newBottom: 0,
      color: "#00ff00",
      angle: '',
      index: null
    };
  },
  methods: {
    mousedown(e) {
      console.log(e.offsetX, e.offsetY);
      // проверка клика по области
      // если ширина и длина отрицательны
      // || ((e.offsetX <= this.containerItems[i].left && e.offsetX >= this.containerItems[i].right)  && (e.offsetY >= this.containerItems[i].bottom && e.offsetY <= this.containerItems[i].top))
      for (let i = this.containerItems.length - 1; i >= 0; i--) {
        if (((e.offsetX >= this.containerItems[i].left && e.offsetX <= this.containerItems[i].left + this.containerItems[i].width) && (e.offsetY >= this.containerItems[i].top && e.offsetY <= this.containerItems[i].top + this.containerItems[i].height)) || ((e.offsetX <= this.containerItems[i].left && e.offsetX >= this.containerItems[i].right) && (e.offsetY >= this.containerItems[i].bottom && e.offsetY <= this.containerItems[i].top)) || ((e.offsetX <= this.containerItems[i].left && e.offsetX >= this.containerItems[i].right) && (e.offsetY >= this.containerItems[i].top && e.offsetY <= this.containerItems[i].top + this.containerItems[i].height)) || ((e.offsetY >= this.containerItems[i].bottom && e.offsetY <= this.containerItems[i].top) && (e.offsetX >= this.containerItems[i].left && e.offsetX <= this.containerItems[i].left + this.containerItems[i].width))) {
          this.flag -= 100000;
          this.changeActive(i);
        }
        // угол который будем двигать у активной области
        if ((e.offsetX <= this.containerItems[i].left + 10 && e.offsetX >= this.containerItems[i].left - 10) && (e.offsetY <= this.containerItems[i].top + 10 && e.offsetY >= this.containerItems[i].top - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'lt'
        } else if ((e.offsetX <= this.containerItems[i].left + 10 && e.offsetX >= this.containerItems[i].left - 10) && (e.offsetY <= this.containerItems[i].bottom + 10 && e.offsetY >= this.containerItems[i].bottom - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'lb'
        } else if ((e.offsetX <= this.containerItems[i].right + 10 && e.offsetX >= this.containerItems[i].right - 10) && (e.offsetY <= this.containerItems[i].top + 10 && e.offsetY >= this.containerItems[i].top - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'rt'
        } else if ((e.offsetX <= this.containerItems[i].right + 10 && e.offsetX >= this.containerItems[i].right - 10) && (e.offsetY <= this.containerItems[i].bottom + 10 && e.offsetY >= this.containerItems[i].bottom - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'rb'
          console.log('rihgt bottom', i);
        }
        else {
          this.flag += 1;
        }
      }
      // верхний левый угол
      this.x = e.offsetX;
      this.y = e.offsetY;
    },
    // рисуем линии
    drawPath(event) {
      window.addEventListener('mouseup', this.drawPathMouseup)
      const canvas = document.getElementById("canvass");
      let ctx = canvas.getContext("2d");
      let x = this.x;
      let y = this.y;
      this.newWidth = event.offsetX - x;
      this.newHeight = event.offsetY - y;
      this.newLeft = x;
      this.newTop = y;
      this.newRight = this.newLeft + this.newWidth;
      this.newBottom = this.newTop + this.newHeight;
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
      if (this.angle === 'rb') {
        // линии
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.lineTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.lineTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.lineTo(event.offsetX, event.offsetY)
        // круг
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.arc(this.containerItems[this.index].left, this.containerItems[this.index].top, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.arc(this.containerItems[this.index].right, this.containerItems[this.index].top, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.arc(this.containerItems[this.index].left, this.containerItems[this.index].bottom, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();

      } else if (this.angle === 'rt') {
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.lineTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.lineTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.lineTo(event.offsetX, event.offsetY)

        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.arc(this.containerItems[this.index].left, this.containerItems[this.index].top, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.arc(this.containerItems[this.index].left, this.containerItems[this.index].bottom, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.arc(this.containerItems[this.index].right, this.containerItems[this.index].bottom, 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();
      } else if (this.angle === 'lt') {
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.lineTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.lineTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.lineTo(event.offsetX, event.offsetY)

        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.arc(this.containerItems[this.index].right, this.containerItems[this.index].top, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].bottom)
        ctx.arc(this.containerItems[this.index].left, this.containerItems[this.index].bottom, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.arc(this.containerItems[this.index].right, this.containerItems[this.index].bottom, 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();
      } else if (this.angle === 'lb') {
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.lineTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.lineTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.lineTo(event.offsetX, event.offsetY)

        ctx.moveTo(this.containerItems[this.index].left, this.containerItems[this.index].top)
        ctx.arc(this.containerItems[this.index].left,this.containerItems[this.index].top, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].top)
        ctx.arc(this.containerItems[this.index].right, this.containerItems[this.index].top, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].right, this.containerItems[this.index].bottom)
        ctx.arc(this.containerItems[this.index].right, this.containerItems[this.index].bottom, 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();
      }
      ctx.closePath()
      ctx.stroke()

    },
    drawPathMouseup(e) {
      window.removeEventListener('mousemove', this.drawPath);
      window.removeEventListener('mouseup', this.drawPathMouseup);
    },
    mouseup(e) {
      this.addNewRect(this.newLeft, this.newTop, this.newWidth, this.newHeight, false);
      this.flag = false;
      this.newHeight = 0;
      this.newWidth = 0;
    },
    mousemove(e) {
      if (this.flag > 0) {
        this.drawRect(e);
      }
    },
    // отрисовка дефолтных четырехугольников
    drawRect(e) {
      if (this.flag >= 0) {
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
        this.newRight = this.newLeft + this.newWidth;
        this.newBottom = this.newTop + this.newHeight;
        // if (this.containerItems.length > 0) {
        //   for (let i = this.containerItems.length - 1; i >= 0; i--) {
        //     if (this.containerItems[i].active === true) {
        //       ctx.arc(this.containerItems[i].left, this.containerItems[i].top, 10, 0, 2 * Math.PI, false);
        //       ctx.moveTo(this.containerItems[i].right, this.containerItems[i].top)
        //       ctx.arc(this.containerItems[i].right, this.containerItems[i].top, 10, 0, 2 * Math.PI, false);
        //       ctx.moveTo(this.containerItems[i].left, this.containerItems[i].bottom)
        //       ctx.arc(this.containerItems[i].left, this.containerItems[i].bottom, 10, 0, 2 * Math.PI, false);
        //       ctx.moveTo(this.containerItems[i].right, this.containerItems[i].bottom)
        //       ctx.arc(this.containerItems[i].right, this.containerItems[i].bottom, 10, 0, 2 * Math.PI, false);
        //       ctx.lineWidth = 1;
        //       ctx.strokeStyle = this.color;
        //       ctx.fillStyle = this.color;
        //       console.log(i);
        //     }
        //   }
        // }
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
    // new object
    addNewRect(left, top, width, height, active) {
      for (let i = this.containerItems.length - 1; i >= 0; i--) {
        this.containerItems[i].active = false
      }
      // отрицательная высота/ширина (реализовано в проверке)
      let push = true;
      // let bottom;
      // let right;
      console.log(left, top, width, height);
      if (height === 0 && width === 0) {
        push = false
        // } else if (height < 0) {
        // } else if (width < 0) {
        // } else if (height < 0 && width < 0) {
        //   // bottom += Math.abs(height);
        //   // top -= Math.abs(height)
        //   // right += Math.abs(width);
        //   // left -= Math.abs(width)
        // } else {
      }
      if (push) {
        this.containerItems.push({
          width: width,
          height: height,
          left: left,
          top: top,
          right: left + width,
          bottom: top + height,
          active: active,
        });
      }
      console.log(this.containerItems);
      //  отрисовывать крайние точки у области с active true
    },
    changeActive(index) {
      for (let i = this.containerItems.length - 1; i >= 0; i--) {
        this.containerItems[i].active = false;
      }
      this.containerItems[index].active = true;
      const canvas = document.getElementById("canvass");
      let ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
      ctx.strokeStyle = this.color
      this.containerItems.forEach((element) => {
        ctx.strokeStyle = "#5eead4";
        ctx.strokeRect(
          element.left,
          element.top,
          element.width,
          element.height
        );
      });
      if (this.containerItems[index].active) {
        ctx.strokeStyle = this.color
        ctx.strokeRect(
          this.containerItems[index].left,
          this.containerItems[index].top,
          this.containerItems[index].width,
          this.containerItems[index].height
        );
      }
      ctx.arc(this.containerItems[index].left, this.containerItems[index].top, 10, 0, 2 * Math.PI, false);
      ctx.moveTo(this.containerItems[index].right, this.containerItems[index].top)
      ctx.arc(this.containerItems[index].right, this.containerItems[index].top, 10, 0, 2 * Math.PI, false);
      ctx.moveTo(this.containerItems[index].left, this.containerItems[index].bottom)
      ctx.arc(this.containerItems[index].left, this.containerItems[index].bottom, 10, 0, 2 * Math.PI, false);
      ctx.moveTo(this.containerItems[index].right, this.containerItems[index].bottom)
      ctx.arc(this.containerItems[index].right, this.containerItems[index].bottom, 10, 0, 2 * Math.PI, false);
      ctx.lineWidth = 1;
      ctx.strokeStyle = this.color;
      ctx.fillStyle = this.color;
      ctx.fill();
    },
  },
};
</script>

<template>
  <div class="">
    <canvas id="canvass" width="1000" height="600" class="bg-slate-800 bg-[url('../public/bg-canvas.jpg')] mx-auto my-4"
      @mousedown="mousedown" @mousemove="mousemove" @mouseup="mouseup">
    </canvas>
  </div>
</template>
<!-- при клике по области менять active, и перерисовывать цвет.... -->