<script>
    import { Node, Svelvet, Minimap, Controls } from "svelvet";

    var basic_node = [
        {
            id: 1,
            position: { x: 50, y: 50 },
            data: {
                label: "addi $t0, $t1, 0\nsubu $t0, $t1, 3",
            },
            width: 175,
            height: 40,
            bgColor: "white",
        },
    ];
</script>

<h1>Welcome to SvelteKit</h1>
<p>
    Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation
</p>

<textarea> Enter MIPS Code Here </textarea>

<Svelvet width={1500} height={500}>
    <Node bgColor="white" id={0} connections={[1, 0]} label="main">
        <div class="node-content">addi $t0, $t1, 1<br /> addi $t0, $t1, 1</div>
    </Node>
    <Node bgColor="white" id={1} position={{ x: 0, y: 100 }}>
        <div class="node-content">addi $t0, $t1, 1<br /> addi $t0, $t1, 1</div>
    </Node>
</Svelvet>

<style>
    .node-content {
        padding: 12px;
        border-radius: 50%;
    }
</style>
