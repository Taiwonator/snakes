<script lang="ts">
    import { onMount } from 'svelte';

    type Coorindate = { x: number, y: number }

    type Actions = {
        increaseSize: () => void
    }

    let lastTime = 0
    let x = 0
    let y = 0
    let width = 20
    let height = 20
    let direction = 1
    let velocity = 0.1
    let snakeBody: Coorindate[] = []
    let actions: Actions

    // We have the heads position. Now what do we want to do, well what comes to mind is to have a snakeBody array. 
    // On on every update, we insert the head into the snakeBody array, and we remove the last element of the array, 
    // Everytime a fooditem is eaten, we increase the length of the snakeBody array.

   onMount(() => {
        const canvas = document.getElementById('gameCanvas') as HTMLCanvasElement;
        const ctx = canvas.getContext('2d');

        const setup = () => {
            actions = {
                increaseSize: () => {
                if(snakeBody.length) {
                    const tail = snakeBody[snakeBody.length - 1]
                    snakeBody.unshift({ x: tail.x, y: tail.y })
                } else {
                    snakeBody.unshift({ x, y })
                }
                console.log('increasing in size', snakeBody)
                snakeBody = snakeBody
            }
            }
            
            // handle Input
            window.addEventListener('keydown', (e) => {
                if(e.key === 'ArrowRight') {
                    direction = 1
                    actions.increaseSize()
                }
            })

            window.addEventListener('keydown', (e) => {
                if(e.key === 'ArrowLeft') {
                    direction = 3
                }
            })

            window.addEventListener('keydown', (e) => {
                if(e.key === 'ArrowUp') {
                    direction = 0
                }
            })

            window.addEventListener('keydown', (e) => {
                if(e.key === 'ArrowDown') {
                    direction = 2
                }
            })

        }

        const gameLoop = (timestamp: number) => {
            const deltaTime = timestamp - lastTime
            lastTime = timestamp    
            update(deltaTime)
            requestAnimationFrame(gameLoop)
        }

        const preRender = () => {
            if(ctx) {
                ctx.clearRect(0, 0, canvas.width, canvas.height)
            }
        }

        const update = (deltaTime: number) => {
            preRender()

            // Calculated the new position of the snake head
            if(ctx) {
                switch(direction) {
                    case 0:
                        y = y - (velocity * deltaTime)
                        break
                    case 1:
                        x =  x + (velocity * deltaTime)
                        break
                    case 2:
                        y = y + (velocity * deltaTime)
                        break
                    case 3:
                        x = x - (velocity * deltaTime)
                        break
                    default: 
                        break
                }
            }

            // Affects the snake body
             snakeBody.unshift({ x, y })
             snakeBody.pop()


            render()
        }

        const render = () => {
            if(ctx) { 
                // Rendering the snake head
                ctx.fillRect(x, y, width, height)

                // Rendering the snake body
                snakeBody.forEach(bodyPart => {
                    ctx.fillRect(bodyPart.x, bodyPart.y, width, height)
                })
            }
        }

        setup()
        requestAnimationFrame(gameLoop)

    })
</script>

<p>snakeBody: {JSON.stringify(snakeBody)}</p>
<canvas id="gameCanvas" class="bg-[green]" width="400" height="400"></canvas>

<style lang="postcss">
  :global(html) {
    background-color: theme(colors.gray.100);
  }
</style>