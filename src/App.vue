<template>
  <div>
    <div id="container"></div>
    <div id="receiver"></div>
    <p>当前小球数量：{{ ballCount }}</p>
  </div>
</template>

<script>
import Matter from "matter-js";

export default {
  data() {
    return {
      engine: null,
      world: null,
      ballCount: 0,
      mouseConstraint: null,
      interv: "",
      images: [
        "https://picsum.photos/50/50?random=1",
        "https://picsum.photos/50/50?random=2",
        "https://picsum.photos/50/50?random=3",
        "https://picsum.photos/50/50?random=4",
        "https://picsum.photos/50/50?random=5",
      ],
    };
  },
  mounted() {
    this.$nextTick(() => {
      const container = document.getElementById("container");
      const receiver = document.getElementById("receiver");
      this.engine = Matter.Engine.create();
      this.world = this.engine.world;
      const render = Matter.Render.create({
        element: container,
        engine: this.engine,
        options: {
          width: container.offsetWidth,
          height: container.offsetHeight,
          wireframes: false,
        },
      });

      Matter.Render.run(render);
      Matter.Engine.run(this.engine);

      // 添加底部接收区域
      Matter.World.add(this.world, [
        Matter.Bodies.rectangle(
          receiver.offsetLeft + receiver.offsetWidth / 2,
          receiver.offsetTop + receiver.offsetHeight / 2,
          receiver.offsetWidth,
          receiver.offsetHeight,
          { isStatic: true, label: "Receiver" }
        ),
        // 添加左侧挡板
        Matter.Bodies.rectangle(
          0,
          container.offsetHeight / 2,
          10,
          container.offsetHeight,
          { isStatic: true, label: "LeftWall" }
        ),
        // 添加右侧挡板
        Matter.Bodies.rectangle(
          container.offsetWidth,
          container.offsetHeight / 2,
          10,
          container.offsetHeight,
          { isStatic: true, label: "RightWall" }
        ),
      ]);

      this.interv = setInterval(() => {
        const randomImage = this.images[
          Math.floor(Math.random() * this.images.length)
        ];
        const ball = Matter.Bodies.rectangle(
          Math.random() * container.offsetWidth,
          0,
          50,
          50,
          {
            friction: 0.005,
            restitution: 0.5,
            density: 0.01,
            label: "Ball",
            render: {
              sprite: {
                texture: randomImage,
                xScale: 1,
                yScale: 1,
                radius: 10, // 图像圆角半径
                type: "roundRect", // 图像类型
              },
              fillStyle: "#ff0000",
              strokeStyle: "#000000",
              lineWidth: 1,
              type: "roundRect",
              radius: 10,
            },
          }
        );
        Matter.World.add(this.world, [ball]);
        this.ballCount++;
      }, 100);

      // 鼠标约束
      this.mouseConstraint = Matter.MouseConstraint.create(this.engine, {
        element: container,
        constraint: {
          render: { visible: false },
        },
      });
      Matter.World.add(this.world, this.mouseConstraint);
    });
  },
  watch: {
    ballCount(newValue, oldValue) {
      // 在ballCount数据变化后更新页面
      // 这里使用了Vue提供的$nextTick()方法来确保DOM元素已被更新
      this.$nextTick(() => {
        if (newValue >= 100) {
          clearInterval(this.interv);
        }
        console.log(`当前小球数量：${newValue}`);
      });
    },
  },
};
</script>

<style>
#container {
  height: 500px;
  width: 500px;
  border: 1px solid black;
}
#receiver {
  height: 50px;
  width: 500px;
  border: 1px solid black;
  margin-top: 20px;
}
img.rounded {
  border-radius: 10px;
  overflow: hidden;
}
</style>
