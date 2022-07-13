<script>
export default {
  data() {
    return {
      flag: 0,
      x: 0,
      y: 0,
      containerItems: [
        {
          width: 0,
          height: 0,
          left: 0,
          top: 0,
          right: 0,
          bottom: 0,
          active: 0,
          lt: [0, 0],
          rt: [0, 0],
          lb: [0, 0],
          rb: [0, 0]
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
      index: null,
      lt: undefined,
      rt: undefined,
      lb: undefined,
      rb: undefined
    };
  },
  methods: {
    mousedown(e) {
      console.log(e.offsetX, e.offsetY);
      // проверка клика по области
      // если ширина и длина отрицательны
      // || ((e.offsetX <= this.containerItems[i].left && e.offsetX >= this.containerItems[i].right)  && (e.offsetY >= this.containerItems[i].bottom && e.offsetY <= this.containerItems[i].top))
      for (let i = this.containerItems.length - 1; i >= 0; i--) {
        this.checkPoint(i, e.offsetX, e.offsetY)
        // угол который будем двигать у активной области
        if ((e.offsetX <= this.containerItems[i].lt[0] + 10 && e.offsetX >= this.containerItems[i].lt[0] - 10) && (e.offsetY <= this.containerItems[i].lt[1] + 10 && e.offsetY >= this.containerItems[i].lt[1] - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'lt'
        } else if ((e.offsetX <= this.containerItems[i].lb[0] + 10 && e.offsetX >= this.containerItems[i].lb[0] - 10) && (e.offsetY <= this.containerItems[i].lb[1] + 10 && e.offsetY >= this.containerItems[i].lb[1] - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'lb'
        } else if ((e.offsetX <= this.containerItems[i].rt[0] + 10 && e.offsetX >= this.containerItems[i].rt[0] - 10) && (e.offsetY <= this.containerItems[i].rt[1] + 10 && e.offsetY >= this.containerItems[i].rt[1] - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'rt'
        } else if ((e.offsetX <= this.containerItems[i].rb[0] + 10 && e.offsetX >= this.containerItems[i].rb[0] - 10) && (e.offsetY <= this.containerItems[i].rb[1] + 10 && e.offsetY >= this.containerItems[i].rb[1] - 10)) {
          window.addEventListener('mousemove', this.drawPath)
          this.flag = -10000;
          this.index = i
          this.angle = 'rb'
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
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
      this.drawAll()
      // this.containerItems.forEach((element) => {
      //   ctx.strokeStyle = "#5eead4";
      //   ctx.moveTo(element.rb[0], element.rb[1])
      //   ctx.lineTo(element.rt[0], element.rt[1])
      //   ctx.moveTo(element.rb[0], element.rb[1])
      //   ctx.lineTo(element.lb[0], element.lb[1])
      //   ctx.moveTo(element.lb[0], element.lb[1])
      //   ctx.lineTo(element.lt[0], element.lt[1])
      //   ctx.moveTo(element.rt[0], element.rt[1])
      //   ctx.lineTo(element.lt[0], element.lt[1])
      //   ctx.closePath()
      //   ctx.stroke()
      // });
      if (this.angle === 'rb') {
        // линии
        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.lineTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.lineTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.lineTo(event.offsetX, event.offsetY)

        // создаем координату сдвинутой крайней точки
        this.containerItems[this.index].rb = [event.offsetX, event.offsetY]

        // круг
        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.arc(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.arc(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.arc(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();
      }
      else if (this.angle === 'rt') {
        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.lineTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.lineTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.lineTo(event.offsetX, event.offsetY)

        this.containerItems[this.index].rt = [event.offsetX, event.offsetY]

        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.arc(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.arc(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.arc(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1], 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();

      } else if (this.angle === 'lt') {
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.lineTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.lineTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.lineTo(event.offsetX, event.offsetY)

        this.containerItems[this.index].lt = [event.offsetX, event.offsetY]

        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.arc(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
        ctx.arc(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.arc(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1], 10, 0, 2 * Math.PI, false);
        ctx.lineWidth = 1;
        ctx.strokeStyle = this.color;
        ctx.fillStyle = this.color;
        ctx.fill();

      } else if (this.angle === 'lb') {
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.lineTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.lineTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.lineTo(event.offsetX, event.offsetY)
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.lineTo(event.offsetX, event.offsetY)

        this.containerItems[this.index].lb = [event.offsetX, event.offsetY]

        ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
        ctx.arc(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
        ctx.arc(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(event.offsetX, event.offsetY)
        ctx.arc(event.offsetX, event.offsetY, 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
        ctx.arc(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1], 10, 0, 2 * Math.PI, false);
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
      if (this.flag > 0) {
        this.addNewRect(this.newLeft, this.newTop, this.newRight, this.newBottom, false);
      }
      // обнуляем
      this.flag = -1;
      this.newHeight = 0;
      this.newWidth = 0;
      this.newLeft = 0;
      this.newRight = 0;
      this.newTop = 0;
      this.newBottom = 0;

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
        this.drawAll();

        this.newWidth = e.offsetX - x;
        this.newHeight = e.offsetY - y;
        this.newLeft = x;
        this.newTop = y;
        this.newRight = this.newLeft + this.newWidth;
        this.newBottom = this.newTop + this.newHeight;
        this.lt = [this.newLeft, this.newTop];
        this.lb = [this.newLeft, this.newBottom]
        this.rt = [this.newRight, this.newTop]
        this.rb = [this.newRight, this.newBottom]

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
    addNewRect(left, top, right, bottom, active) {
      for (let i = this.containerItems.length - 1; i >= 0; i--) {
        this.containerItems[i].active = false
      }
      let push = true;
      // не пушим при клике по пустой области
      if (Math.abs(left - right) === 0 && Math.abs(top - bottom) === 0) {
        push = false
      }
      if (push) {
        this.containerItems.push({
          width: right - left,
          height: bottom - top,
          left: left,
          top: top,
          right: right,
          bottom: bottom,
          active: active,
          lt: this.lt,
          rt: this.rt,
          lb: this.lb,
          rb: this.rb
        });
      }
      console.log(this.containerItems);
      //  отрисовывать крайние точки у области с active true
    },
    drawAll() {
      const canvas = document.getElementById("canvass");
      let ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
      this.containerItems.forEach((element) => {
        ctx.strokeStyle = "#5eead4";
        ctx.moveTo(element.rb[0], element.rb[1])
        ctx.lineTo(element.rt[0], element.rt[1])
        ctx.moveTo(element.rb[0], element.rb[1])
        ctx.lineTo(element.lb[0], element.lb[1])
        ctx.moveTo(element.lb[0], element.lb[1])
        ctx.lineTo(element.lt[0], element.lt[1])
        ctx.moveTo(element.rt[0], element.rt[1])
        ctx.lineTo(element.lt[0], element.lt[1])
        ctx.closePath()
        ctx.stroke()
      });
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
      this.drawAll();
      if (this.containerItems[index].active) {
        ctx.strokeStyle = this.color
        ctx.moveTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
        ctx.lineTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
        ctx.moveTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
        ctx.lineTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
        ctx.moveTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
        ctx.lineTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])
        ctx.moveTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
        ctx.lineTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])

        ctx.moveTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
        ctx.arc(this.containerItems[index].rb[0], this.containerItems[index].rb[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
        ctx.arc(this.containerItems[index].rt[0], this.containerItems[index].rt[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
        ctx.arc(this.containerItems[index].lb[0], this.containerItems[index].lb[1], 10, 0, 2 * Math.PI, false);
        ctx.moveTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])
        ctx.arc(this.containerItems[index].lt[0], this.containerItems[index].lt[1], 10, 0, 2 * Math.PI, false);
        ctx.closePath()
        ctx.stroke()
      }
      ctx.lineWidth = 1;
      ctx.strokeStyle = this.color;
      ctx.fillStyle = this.color;
      ctx.fill();
    },
    checkPoint(index, x, y) {
      const canvas = document.getElementById("canvass");
      let ctx = canvas.getContext("2d");

      ctx.beginPath()
      ctx.moveTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])
      ctx.lineTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
      ctx.lineTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
      ctx.closePath()
      if (ctx.isPointInPath(x, y)) {
        this.flag -= 100000;
        this.changeActive(index);
        console.log('yes');
      }

      ctx.moveTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
      ctx.lineTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])
      ctx.lineTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
      ctx.closePath()
      if (ctx.isPointInPath(x, y)) {
        this.flag -= 100000;
        this.changeActive(index);
        console.log('yes');
      };

      ctx.moveTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
      ctx.lineTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
      ctx.lineTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
      ctx.closePath()
      if (ctx.isPointInPath(x, y)) {
        this.flag -= 100000;
        this.changeActive(index);
        console.log('yes');
      };
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
<!-- 1) разбить на треугольник и реализация с помощью треугольников http://cyber-code.ru/tochka_v_treugolnike/ -->