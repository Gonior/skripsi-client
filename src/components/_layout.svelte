<script>
	import Header from "./Header.svelte";
	import Sidebar from './Sidebar.svelte'
    import Homepage from './Homepage.svelte'	
	
</script>

<input id="my-drawer" type="checkbox" class="drawer-toggle">
<div class="drawer-content max-w-md w-full mx-auto">
    <Header>
        <label for="my-drawer" class="btn btn-ghost btn-square rounded-xl bg-base-200 drawer-button">
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="text-base-content h-6 w-6" ><path d="M9 18h12M3 12h18M3 6h12" style="fill:none;stroke:currentColor;stroke-linecap:round;stroke-linejoin:round;stroke-width:2"/></svg>
        </label>
    </Header>
   <Homepage />
</div>
<div class="drawer-side">
    <label for="my-drawer" class="drawer-overlay"></label>
    <Sidebar />
</div>


