<script>
  import Snake from "./Snake.svelte";
  import Food from "./Food.svelte";
  let foodLeft = 0;
  let foodTop = 0;
  let direction = "right";
  let snakeBodies = [];

  $: score = snakeBodies.length - 3;

  setInterval(() => {
    snakeBodies.pop();

    let { top, left } = snakeBodies[0];

    if (direction === "up") {
      top -= 50;
    } else if (direction === "down") {
      top += 50;
    } else if (direction === "left") {
      left -= 50;
    } else if (direction === "right") {
      left += 50;
    }

    const newHead = { top, left };

    snakeBodies = [newHead, ...snakeBodies];

    if (isCollide(newHead, { left: foodLeft, top: foodTop })) {
      foodMove();
      snakeBodies = [...snakeBodies, snakeBodies[snakeBodies.length - 1]];
    }

    if (isGameOver()) {
      resetGame();
    }
  }, 200);

  function foodMove() {
    foodTop = Math.floor(Math.random() * 10) * 50;
    foodLeft = Math.floor(Math.random() * 20) * 50;
  }

  function resetGame() {
    foodMove();
    direction = "right";
    snakeBodies = [
      {
        left: 100,
        top: 0
      },
      {
        left: 50,
        top: 0
      },
      {
        left: 0,
        top: 0
      }
    ];
  }

  function isGameOver() {
    const snakeBodiesNoHead = snakeBodies.slice(1);

    const snakeCollisions = snakeBodiesNoHead.filter(sb =>
      isCollide(sb, snakeBodies[0])
    );

    if (snakeCollisions.length > 0) {
      return true;
    }

    const { top, left } = snakeBodies[0];

    if (top >= 500 || top < 0 || left < 0 || left >= 1000) {
      return true;
    }

    return false;
  }

  function isCollide(a, b) {
    return !(
      a.top < b.top ||
      a.top > b.top ||
      a.left < b.left ||
      a.left > b.left
    );
  }

  function getDirectionFromKeyCode(keyCode) {
    if (keyCode === 38) {
      return "up";
    } else if (keyCode === 39) {
      return "right";
    } else if (keyCode === 37) {
      return "left";
    } else if (keyCode === 40) {
      return "down";
    }

    return false;
  }

  function onKeyDown(e) {
    e.preventDefault();
    const newDirection = getDirectionFromKeyCode(e.keyCode);
    if (newDirection) {
      direction = newDirection;
    }
  }

  resetGame();
</script>

<style>
  main {
    width: 1000px;
    height: 500px;
    border: solid black 1px;
    position: relative;
    margin: 20px auto;
    background-image: url("../background.jpg");
    background-size: cover;
  }
  h1,
  h2,
  p {
    text-align: center;
  }
</style>

<h1>Mali Molly's Snake Game</h1>
<p>Not my cat. She's a rental.</p>
<main>
  <Snake {snakeBodies} {direction} />
  <Food {foodLeft} {foodTop} />
</main>
<h2>Score: {score}</h2>
<svelte:window on:keydown={onKeyDown} />
