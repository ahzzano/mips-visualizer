<script>
    import { Node, Svelvet, Edge, Anchor } from "svelvet";
    import Label from "../components/Label.svelte";

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
    var edges = [{ source: 0, target: 1 }];

    const connections = [1];
</script>

<h1>Welcome to SvelteKit</h1>
<p>
    Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation
</p>

<textarea> Enter MIPS Code Here </textarea>

<Svelvet
    width={1500}
    height={500}
    edgeStyle="step"
    TD={true}
    edgesAboveNode
    endStyles={[null, "arrow"]}
>
    <Node useDefaults id={"main"}>
        <div class="node-content">
            <div>main:</div>
            <div>addi $t0, $t1, $2</div>
            <div>addi $t0, $t1, $2</div>
        </div>
        <div class="out-anchors">
            <Anchor id={"main-out"} connections={["falin"]}>
                <Label />
            </Anchor>
        </div>
    </Node>

    <Node useDefaults id={"falin"} position={{ x: 0, y: 250 }}>
        <div class="in-anchors">
            <Anchor id={"falin-in"}><Label /></Anchor>
        </div>
        <div class="node-content">
            <div>falin:</div>
            <div>addi $t0, $t1, $2</div>
            <div>addi $t0, $t1, $2</div>
            <div>addi $t0, $t1, $2</div>
        </div>
    </Node>
</Svelvet>

<style>
    .node-content {
        width: fit-content;
        padding: 10px;
        position: relative;
        display: flex;
        flex-direction: column;
        box-shadow: none;
    }

    .out-anchors {
        position: absolute;
        right: -24px;
        top: 8px;
        display: flex;
    }

    .in-anchors {
        display: flex;
        position: absolute;
        right: -24px;
    }
</style>
