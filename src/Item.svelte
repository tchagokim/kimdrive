<script>
    import {createEventDispatcher} from "svelte"
    const dsp = createEventDispatcher()

    import Icone from "./Icone.svelte"
    export let item = null

    const fn_split = item.name.split(".")
    const extensao = fn_split[fn_split.length-1]

    const click = () => dsp("click",item)
    const deletar = () => dsp("deletar",item)
    const renomear = () => dsp("renomear",item)
    const download = () => dsp("download",item)

    export let path = false
</script>

<div class="item-main" >
    <div class="item-icone">
        <span>
            <Icone extensao={extensao} dir={item.dir} />
        </span>
        
    </div>
    <div class="item-name" on:click={click}>
        <span>{item.name}</span>
    </div>
    <div class="item-size">
        {#if item.dir==false}
        <span>{(item.size/1000).toFixed(2)} KB</span>
        {/if}
    </div>
    <div class="item-type">
        <span>{extensao!=item.name ? extensao : ""}</span>
    </div>
    {#if path==false}
    <div class="item-actions">
        <button on:click={renomear} class="btn btn-sm btn-outline-primary">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M20 14.66V20a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h5.34"></path><polygon points="18 2 22 6 12 16 8 16 8 12 18 2"></polygon></svg>
        </button>
        <button on:click={deletar} class="btn btn-sm btn-outline-danger">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polyline points="3 6 5 6 21 6"></polyline><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"></path><line x1="10" y1="11" x2="10" y2="17"></line><line x1="14" y1="11" x2="14" y2="17"></line></svg>
        </button>
        <button on:click={download} class="btn btn-sm btn-info"
        class:disabled={item.dir==true}
        >
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21.2 15c.7-1.2 1-2.5.7-3.9-.6-2-2.4-3.5-4.4-3.5h-1.2c-.7-3-3.2-5.2-6.2-5.6-3-.3-5.9 1.3-7.3 4-1.2 2.5-1 6.5.5 8.8M12 19.8V12M16 17l-4 4-4-4"/></svg>
        </button>

    </div>
    {:else}
    <div class="d-flex align-items-center">
        <span class="me-2">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"></path></svg>
        </span>
        <span>{item.path}</span>
    </div>
      
    {/if}
</div>

<style>
    .item-main {
	display: grid;
	grid-template-columns: auto 1fr auto auto auto;
	gap:12px;
	align-items: center;
	justify-items: start;
	margin-bottom: 16px;
	
}
.item-main span{
	font-size: 20px;

}
.item-main:hover {
	cursor: pointer;
	color: rgb(45, 79, 163);
}

</style>