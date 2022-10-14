<script>
    import {fly} from 'svelte/transition'
    import {createEventDispatcher, onMount} from 'svelte'
    import axios from 'axios'
    import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';
    import {BASE_URL} from '../lib/store'
    import {SyncLoader} from 'svelte-loading-spinners'
    export let show = false
    export let data
    let text 

    let isLoading = true,
        state = "",
        err = {
            isError : false,
            message : "",
            code : 0
        }
    const progress = tweened(0, {
		duration: 100,
		easing: cubicOut
	});
    const dispatch = createEventDispatcher()

    onMount( async () => {
        window.history.pushState({id :1},null,null)
        window.addEventListener('popstate', (e) => {
            e.preventDefault()
            back()
        })
        console.log(data)
        await handleScan(data)
    })
    const handleClose = () => {
        dispatch('close', {show : false})
    }

    const getSize = (data) => {
        let stringLength = data.length - 'data:image/jpeg;base64,'.length;
        let sizeInBytes = 4 * Math.ceil((stringLength / 3));

        return sizeInBytes

    }
    const handleScan = async (data) => {
        const config = {
            onUploadProgress : progressEvent => {
                if (progressEvent.loaded === progressEvent.total) state = "Sedang memproses"
                else state = "Mengunggah"
                progress.set(progressEvent.loaded/progressEvent.total*100)
            }
        }
        //axios here
        
        let req = await axios.post(`${BASE_URL}/upload`, {image : data}, config).catch(error => {
            err.isError = true
            if (error.response) {
                // The request was made and the server responded with a status code
                // that falls out of the range of 2xx
                err.message = error.response.data.message
                err.code = error.response.status
            } else if (error.request) {
                // The request was made but no response was received
                // `error.request` is an instance of XMLHttpRequest in the browser and an instance of
                // http.ClientRequest in node.js
                err.message = JSON.stringify(error.request)
                err.code = 500
            } else {
                // Something happened in setting up the request that triggered an Error
                err.message = error.message
            }
            return
        })
        if (req) {
            console.log(req)
            text = req
        }
        isLoading = false
    }

</script>

{#if show}
<div class="absolute inset-0 z-20 flex bg-base-100 bg-opacity-80 justify-center items-center">
    <div in:fly={{y :200 , duration : 200}} class="flex flex-col transition-all duration-500 ease-in-out {isLoading || err.isError ? 'min-h-max max-h-80 h-1/4 min-w-max w-1/2 max-w-md p-4 bg-base-200 rounded-lg items-center justify-center' : "h-full w-full p-0 bg-base-100"} ">
        
        {#if isLoading}
            <SyncLoader color="#5B31CE" size="5" unit="rem" />
            <span class="text-base-content text-opacity-80 capitalize">{state}</span>
            <progress class="progress progress-primary w-56" value="{$progress}" max="100"></progress>
            <span class="text-base-content text-opacity-80 capitalize">{Math.round($progress)}%</span>
        {:else if err.isError}
        
            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" class="h-24 w-24 text-error text-opacity-80" fill="currentColor" stroke="none"><path d="M12 2a10 10 0 1 0 10 10A10 10 0 0 0 12 2Zm0 16a1 1 0 1 1 1-1 1 1 0 0 1-1 1Zm1-5a1 1 0 0 1-2 0V7a1 1 0 0 1 2 0Z"/></svg>
            <h1 class="font-bold text-xl text-base-content">Gagal Menerjemahkan</h1>
            <span class="text-base-content text-center max-w-xs text-opacity-80 capitalize">{err.message}</span>
            <button class="btn btn-ghost btn-sm " on:click={() => handleClose()}>Tutup</button>
            
        {:else}

        <h1>result will appear here</h1>
        {JSON.stringify(text)}
        {/if}
    </div>
</div>
{/if}

<!-- <button on:click={() => handleClose()} class="w-1/2 btn btn-primary btn-sm block rounded-full">Oke</button> -->