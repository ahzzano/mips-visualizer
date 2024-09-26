<script lang="ts">
    import { Node, Svelvet, Anchor } from "svelvet";
    import Label from "../components/Label.svelte";

    class Block {
        label: string = "";
        content: string[] = [];
    }
    let mips_code = "main: ";

    // each code block has {label: string, content}
    var code_blocks: Block[] = [];

    function parseMipsCode() {
        code_blocks = [];
        console.log(mips_code);
        let mipsLines: string[] = mips_code.split("\n");

        let block = new Block();
        for (const line in mipsLines) {
            if (mipsLines[line].includes(":")) {
                console.log(mipsLines);
                code_blocks.push(block);
                block = new Block();
                let labelName = mipsLines[line].split(":");
                block.label = labelName[0];
                let others = labelName[1].trim();
                if (others != "") {
                    block.content.push(others);
                }
                continue;
            }

            block.content.push(mipsLines[line]);
        }
        code_blocks.push(block);
        console.log(code_blocks);
    }
</script>

<h1>Welcome to SvelteKit</h1>
<p>
    Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation
</p>

<textarea bind:value={mips_code} />
<button on:click={parseMipsCode}>Visualize!</button>

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
            <Anchor id={"falin-in"} direction={"north"}><Label /></Anchor>
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
        justify-content: center;
    }
</style>
