<template>
  <div class="">
    <canvas id="canvass" width="1000" height="600" class="bg-slate-800 bg-[url('../public/bg-canvas.jpg')] mx-auto my-4"
      @mousedown="mousedown" @mousemove="mousemove" @mouseup="mouseup" ref="canvas">
    </canvas>
  </div>
</template>

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
      rb: undefined,
      interest: false
    };
  },
  methods: {
    mousedown(e) {
      // проверка клика по области
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
      this.changeActive(this.index)
      window.addEventListener('mouseup', this.drawPathMouseup)
      const canvas = this.$refs.canvas
      let ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      this.drawAll(true)
      ctx.beginPath();

      if (this.angle === 'rb') {
        // создаем координату сдвинутой крайней точки
        this.containerItems[this.index].rb = [event.offsetX, event.offsetY]
      } else if (this.angle === 'rt') {
        this.containerItems[this.index].rt = [event.offsetX, event.offsetY]

      } else if (this.angle === 'lt') {
        this.containerItems[this.index].lt = [event.offsetX, event.offsetY]

      } else if (this.angle === 'lb') {
        this.containerItems[this.index].lb = [event.offsetX, event.offsetY]
      }
      ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
      ctx.lineTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
      ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
      ctx.lineTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
      ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
      ctx.lineTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
      ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
      ctx.lineTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])

      ctx.moveTo(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1])
      ctx.arc(this.containerItems[this.index].lt[0], this.containerItems[this.index].lt[1], 10, 0, 2 * Math.PI, false);
      ctx.moveTo(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1])
      ctx.arc(this.containerItems[this.index].rt[0], this.containerItems[this.index].rt[1], 10, 0, 2 * Math.PI, false);
      ctx.moveTo(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1])
      ctx.arc(this.containerItems[this.index].lb[0], this.containerItems[this.index].lb[1], 10, 0, 2 * Math.PI, false)
      ctx.moveTo(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1])
      ctx.arc(this.containerItems[this.index].rb[0], this.containerItems[this.index].rb[1], 10, 0, 2 * Math.PI, false);

      ctx.lineWidth = 4;

      this.interest = false;

      this.lineInterest(this.containerItems[this.index].lt, this.containerItems[this.index].rt, this.containerItems[this.index].lb, this.containerItems[this.index].rb)
      if (this.interest) {
        ctx.strokeStyle = "red";
        ctx.fillStyle = "red";
      }
      else {
        ctx.fillStyle = this.color;
        ctx.strokeStyle = this.color;
      }
      ctx.closePath()
      ctx.stroke()
      ctx.fill();

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
        const canvas = this.$refs.canvas
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
        ctx.lineWidth = 4;
        ctx.fill();

        this.interest = false;
        this.lineInterest(this.lt, this.rt, this.lb, this.rb)
        if (this.interest) {
          console.log('yes');
          ctx.strokeStyle = "red";
          ctx.fillStyle = "red";
        }
        else {
          ctx.fillStyle = this.color;
          ctx.strokeStyle = this.color;
        }
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
    },
    drawAll(activeItem) {
      const canvas = this.$refs.canvas
      let ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.beginPath();
      this.containerItems.forEach((element) => {
        if (activeItem && element.active) {

        } else {
          ctx.strokeStyle = "#5eead4";
          ctx.moveTo(element.rb[0], element.rb[1])
          ctx.lineTo(element.rt[0], element.rt[1])
          ctx.moveTo(element.rb[0], element.rb[1])
          ctx.lineTo(element.lb[0], element.lb[1])
          ctx.moveTo(element.lb[0], element.lb[1])
          ctx.lineTo(element.lt[0], element.lt[1])
          ctx.moveTo(element.rt[0], element.rt[1])
          ctx.lineTo(element.lt[0], element.lt[1])
          ctx.lineWidth = 1;
          ctx.closePath()
          ctx.stroke()
        }
      });
    },
    changeActive(index) {
      for (let i = this.containerItems.length - 1; i >= 0; i--) {
        this.containerItems[i].active = false;
      }
      this.containerItems[index].active = true;
      const canvas = this.$refs.canvas
      let ctx = canvas.getContext("2d");
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      this.drawAll(true);
      ctx.beginPath();
      if (this.containerItems[index].active) {
        this.interest = false;
        this.lineInterest(this.containerItems[index].lt, this.containerItems[index].rt, this.containerItems[index].lb, this.containerItems[index].rb)
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

        ctx.lineWidth = 4;
        if (this.interest) {
          ctx.strokeStyle = "red";
          ctx.fillStyle = "red";
        }
        else {
          ctx.fillStyle = this.color;
          ctx.strokeStyle = this.color;
        }
        ctx.closePath()
        ctx.stroke()
        ctx.fill();
      }
    },
    checkPoint(index, x, y) {
      const canvas = this.$refs.canvas
      let ctx = canvas.getContext("2d");

      ctx.beginPath()
      ctx.moveTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])
      ctx.lineTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
      ctx.lineTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
      ctx.closePath()
      if (ctx.isPointInPath(x, y)) {
        this.flag -= 100000;
        this.changeActive(index);
      }

      ctx.moveTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
      ctx.lineTo(this.containerItems[index].lt[0], this.containerItems[index].lt[1])
      ctx.lineTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
      ctx.closePath()
      if (ctx.isPointInPath(x, y)) {
        this.flag -= 100000;
        this.changeActive(index);
      };

      ctx.moveTo(this.containerItems[index].rb[0], this.containerItems[index].rb[1])
      ctx.lineTo(this.containerItems[index].rt[0], this.containerItems[index].rt[1])
      ctx.lineTo(this.containerItems[index].lb[0], this.containerItems[index].lb[1])
      ctx.closePath()
      if (ctx.isPointInPath(x, y)) {
        this.flag -= 100000;
        this.changeActive(index);
      };
    },
    lineInterest(lt, rt, lb, rb) {

      let det = 0;
      let gamma = 0;
      let lambda = 0;
      // if (this.containerItems.length > 0) {
      for (let element = this.containerItems.length - 1;
        element >= 1;
        element--) {
        // lt rt пересекается с lt rt
        if (this.interest) {
          return
        }
        det = (rt[0] - lt[0]) * (this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].rt[0] - this.containerItems[element].lt[0]) * (rt[1] - lt[1])
        if (det === 0) {
          this.interest = false;
        } else {
          lambda = ((this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].rt[0] - lt[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].rt[0]) * (this.containerItems[element].rt[1] - lt[1])) / det;
          gamma = ((lt[1] - rt[1]) * (this.containerItems[element].rt[0] - lt[0]) + (rt[0] - lt[0]) * (this.containerItems[element].rt[1] - lt[1])) / det;
          this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
          if (this.interest) {
            det = 0;
            lambda = 0;
            gamma = 0;
          }
        }
        // from (a,b)->(c,d) intersects with (p,q)->(r,s)
        // var det, gamma, lambda;
        // det = (c - a) * (s - q) - (r - p) * (d - b);
        // if (det === 0) {
        //   return false;
        // } else {
        //   lambda = ((s - q) * (r - a) + (p - r) * (s - b)) / det;
        //   gamma = ((b - d) * (r - a) + (c - a) * (s - b)) / det;
        //   return (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1);
        // }
        if (this.interest === false) {
          // lt rt с rt rb
          det = (rt[0] - lt[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].rt[0]) * (rt[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) * (this.containerItems[element].rb[0] - lt[0]) + (this.containerItems[element].rt[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            gamma = ((lt[1] - rt[1]) * (this.containerItems[element].rb[0] - lt[0]) + (rt[0] - lt[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lt rt с lt lb
          det = (rt[0] - lt[0]) * (this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].lb[0] - this.containerItems[element].lt[0]) * (rt[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].lb[0] - lt[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].lb[0]) * (this.containerItems[element].lb[1] - lt[1])) / det;
            gamma = ((lt[1] - rt[1]) * (this.containerItems[element].lb[0] - lt[0]) + (rt[0] - lt[0]) * (this.containerItems[element].lb[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lt rt с lb rb
          det = (rt[0] - lt[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].lb[0]) * (rt[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) * (this.containerItems[element].rb[0] - lt[0]) + (this.containerItems[element].lb[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            gamma = ((lt[1] - rt[1]) * (this.containerItems[element].rb[0] - lt[0]) + (rt[0] - lt[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lt lb с lt rt
          det = (lb[0] - lt[0]) * (this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].rt[0] - this.containerItems[element].lt[0]) * (lb[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].rt[0] - lt[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].rt[0]) * (this.containerItems[element].rt[1] - lt[1])) / det;
            gamma = ((lt[1] - lb[1]) * (this.containerItems[element].rt[0] - lt[0]) + (lb[0] - lt[0]) * (this.containerItems[element].rt[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lt lb с rt rb
          det = (lb[0] - lt[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].rt[0]) * (lb[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) * (this.containerItems[element].rb[0] - lt[0]) + (this.containerItems[element].rt[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            gamma = ((lt[1] - lb[1]) * (this.containerItems[element].rb[0] - lt[0]) + (lb[0] - lt[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lt lb с lt lb

          det = (lb[0] - lt[0]) * (this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].lb[0] - this.containerItems[element].lt[0]) * (lb[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].lb[0] - lt[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].lb[0]) * (this.containerItems[element].lb[1] - lt[1])) / det;
            gamma = ((lt[1] - lb[1]) * (this.containerItems[element].lb[0] - lt[0]) + (lb[0] - lt[0]) * (this.containerItems[element].lb[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lt lb с lb rb
          det = (lb[0] - lt[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].lb[0]) * (lb[1] - lt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) * (this.containerItems[element].rb[0] - lt[0]) + (this.containerItems[element].lb[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            gamma = ((lt[1] - lb[1]) * (this.containerItems[element].rb[0] - lt[0]) + (lb[0] - lt[0]) * (this.containerItems[element].rb[1] - lt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // rt rb с lt rt
          det = (rb[0] - rt[0]) * (this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].rt[0] - this.containerItems[element].lt[0]) * (rb[1] - rt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].rt[0] - rt[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].rt[0]) * (this.containerItems[element].rt[1] - rt[1])) / det;
            gamma = ((rt[1] - rb[1]) * (this.containerItems[element].rt[0] - rt[0]) + (rb[0] - rt[0]) * (this.containerItems[element].rt[1] - rt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // rt rb с rt rb

          det = (rb[0] - rt[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].rt[0]) * (rb[1] - rt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) * (this.containerItems[element].rb[0] - rt[0]) + (this.containerItems[element].rt[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - rt[1])) / det;
            gamma = ((rt[1] - rb[1]) * (this.containerItems[element].rb[0] - rt[0]) + (rb[0] - rt[0]) * (this.containerItems[element].rb[1] - rt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // rt rb с lt lb

          det = (rb[0] - rt[0]) * (this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].lb[0] - this.containerItems[element].lt[0]) * (rb[1] - rt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].lb[0] - rt[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].lb[0]) * (this.containerItems[element].lb[1] - rt[1])) / det;
            gamma = ((rt[1] - rb[1]) * (this.containerItems[element].lb[0] - rt[0]) + (rb[0] - rt[0]) * (this.containerItems[element].lb[1] - rt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // rt rb с lb rb

          det = (rb[0] - rt[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].lb[0]) * (rb[1] - rt[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) * (this.containerItems[element].rb[0] - rt[0]) + (this.containerItems[element].lb[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - rt[1])) / det;
            gamma = ((rt[1] - rb[1]) * (this.containerItems[element].rb[0] - rt[0]) + (rb[0] - rt[0]) * (this.containerItems[element].rb[1] - rt[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lb rb с lt rt
          det = (rb[0] - lb[0]) * (this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].rt[0] - this.containerItems[element].lt[0]) * (rb[1] - lb[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rt[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].rt[0] - lb[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].rt[0]) * (this.containerItems[element].rt[1] - lb[1])) / det;
            gamma = ((lb[1] - rb[1]) * (this.containerItems[element].rt[0] - lb[0]) + (rb[0] - lb[0]) * (this.containerItems[element].rt[1] - lb[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lb rb с rt rb

          det = (rb[0] - lb[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].rt[0]) * (rb[1] - lb[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].rt[1]) * (this.containerItems[element].rb[0] - lb[0]) + (this.containerItems[element].rt[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - lb[1])) / det;
            gamma = ((lb[1] - rb[1]) * (this.containerItems[element].rb[0] - lb[0]) + (rb[0] - lb[0]) * (this.containerItems[element].rb[1] - lb[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lb rb с lt lb
          det = (rb[0] - lb[0]) * (this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) - (this.containerItems[element].lb[0] - this.containerItems[element].lt[0]) * (rb[1] - lb[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].lb[1] - this.containerItems[element].lt[1]) * (this.containerItems[element].lb[0] - lb[0]) + (this.containerItems[element].lt[0] - this.containerItems[element].lb[0]) * (this.containerItems[element].lb[1] - lb[1])) / det;
            gamma = ((lb[1] - rb[1]) * (this.containerItems[element].lb[0] - lb[0]) + (rb[0] - lb[0]) * (this.containerItems[element].lb[1] - lb[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
        if (this.interest === false) {
          // lb rb с lb rb
          det = (rb[0] - lb[0]) * (this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) - (this.containerItems[element].rb[0] - this.containerItems[element].lb[0]) * (rb[1] - lb[1])
          if (det === 0) {
            this.interest = false;
          } else {
            lambda = ((this.containerItems[element].rb[1] - this.containerItems[element].lb[1]) * (this.containerItems[element].rb[0] - lb[0]) + (this.containerItems[element].lb[0] - this.containerItems[element].rb[0]) * (this.containerItems[element].rb[1] - lb[1])) / det;
            gamma = ((lb[1] - rb[1]) * (this.containerItems[element].rb[0] - lb[0]) + (rb[0] - lb[0]) * (this.containerItems[element].rb[1] - lb[1])) / det;
            this.interest = (0 < lambda && lambda < 1) && (0 < gamma && gamma < 1)
            if (this.interest) {
              det = 0;
              lambda = 0;
              gamma = 0;
            }
          }
        }
      }

    },
  },
};
</script>

<!-- https://stackovergo.com/ru/q/2326403/test-if-two-lines-intersect---javascript-function
    сделать проверку на пересечение каждого отрезка
 -->
 <!-- 1) строим область , делаем проверку на пересечение по линиям или точкам
 2) если есть пересечение пересекаемая область становится красной
 3) елси нет она остается зеленой
 4) при растягивании точки делаем проверку на пересечение по л или т, если true делаем красным, иначе остается зеленой -->