<script lang="ts">
    import { Node, Svelvet, Anchor } from "svelvet";
    import Label from "../components/Label.svelte";

    class CodeBlock {
        label: string = "";
        id: number = 0;
        lines: CodeLine[] = [];
        has_inputs: boolean = false;
    }

    class CodeLine {
        line: string = "";
        branchesTo: number = -1;
    }

    let mips_code = "";

    let blocks_of_code: CodeBlock[] = [];

    const jump_instructions = ["j", "jal"];
    const branch_instructions = [];

    function parse_mips() {
        blocks_of_code = [];
        let lines = mips_code.split("\n");

        let current_block = new CodeBlock();
        current_block.id = 0;

        let label_ids: { [label: string]: number } = {};
        label_ids[""] = 0;

        // main: addi $t0, $t1, 2
        const labels = lines.filter((line) => line.includes(":"));

        let current_id = 0;
        for (const label of labels) {
            label_ids[label.split(":")[0]] = current_id;
            current_id += 1;
        }
        console.log(label_ids);

        for (const line of lines) {
            if (line == "" || line.length == 0) {
                continue;
            }

            if (!line.includes(":")) {
                let cline = new CodeLine();
                cline.line = line;

                let text = line.split(" ");

                if (jump_instructions.includes(text[0])) {
                    cline.branchesTo = label_ids[text[1].trim()];
                }

                current_block.lines.push(cline);
            } else {
                blocks_of_code.push(current_block);
                let v = line.split(":");

                let label = v[0];
                let content = v[1];

                current_block = new CodeBlock();
                current_block.label = label;
                current_block.id = label_ids[label];

                if (content != undefined) {
                    let cline = new CodeLine();
                    cline.line = content.trim();
                    current_block.lines.push(cline);
                }

                label_ids[label] = current_id;
            }
        }

        blocks_of_code = blocks_of_code.filter(
            (block) => block.lines.length != 0,
        );

        for (const block of blocks_of_code) {
            if (block.label != "") {
                block.id = label_ids[block.label];
            }
        }
        blocks_of_code.sort((a, b) => (a.id > b.id ? a.id : b.id));

        blocks_of_code.push(current_block);
    }
</script>

<h1>Welcome to SvelteKit</h1>
<p>
    Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation
</p>

<textarea bind:value={mips_code} />
<button on:click={parse_mips}> parse</button>

<Svelvet
    width={1500}
    height={500}
    edgeStyle="step"
    theme="dark"
    TD={true}
    edgesAboveNode
    endStyles={[null, "arrow"]}
>
    {#each blocks_of_code as block, index}
        <Node useDefaults id={block.id} position={{ x: 0, y: index * 50 }}>
            <div class="in-anchors">
                <Anchor direction="north" id={"in"} multiple></Anchor>
            </div>
            <div class="node-content">
                {#if block.label != ""}
                    {block.label} <br />
                    <br />
                {/if}
                {#each block.lines as line}
                    {#if line.branchesTo != -1 && line.branchesTo != undefined}
                        <div class="linking">
                            <span>
                                {line.line}
                            </span>
                            <div class="anchor-content">
                                <Anchor connections={[[line.branchesTo, "in"]]}
                                ></Anchor>
                            </div>
                        </div>
                    {:else}
                        <span>
                            {line.line}
                        </span>
                    {/if}
                {/each}
            </div>
        </Node>
    {/each}

    <!-- <Node useDefaults id={"main"}> -->
    <!--     <div class="node-content"> -->
    <!--         <div>main:</div> -->
    <!--         <div>addi $t0, $t1, $2</div> -->
    <!--         <div>addi $t0, $t1, $2</div> -->
    <!--     </div> -->
    <!--     <div class="out-anchors"> -->
    <!--         <Anchor -->
    <!--             id={"main-out"} -->
    <!--             connections={[["falin", "falin-in"]]} -->
    <!--             direction={"south"} -->
    <!--         > -->
    <!--             <Label /> -->
    <!--         </Anchor> -->
    <!--     </div> -->
    <!-- </Node> -->
    <!---->
    <!-- <Node useDefaults id={"falin"} position={{ x: 0, y: 250 }}> -->
    <!--     <div class="in-anchors"> -->
    <!--         <Anchor id={"falin-in"} direction={"north"}><Label /></Anchor> -->
    <!--     </div> -->
    <!--     <div class="node-content"> -->
    <!--         <div>falin:</div> -->
    <!--         <div>addi $t0, $t1, $2</div> -->
    <!--         <div>addi $t0, $t1, $2</div> -->
    <!--         <div>addi $t0, $t1, $2</div> -->
    <!--     </div> -->
    <!-- </Node> -->
</Svelvet>

<style>
    .node-content {
        width: fit-content;
        padding: 10px;
        position: relative;
        display: flex;
        flex-direction: column;
        justify-content: center;
        box-shadow: none;
    }

    .out-anchors {
        position: absolute;
        display: flex;
        justify-content: center;
    }

    .in-anchors {
        display: flex;
        justify-content: center;
    }

    .anchor-content {
        position: absolute;
        width: 25px;
        height: 25px;
        right: -27px;
    }

    .linking {
        display: flex;
        flex-direction: row;
    }
</style>
