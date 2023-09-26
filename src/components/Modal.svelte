<script>
	export let visible, width = "", height = ""; // boolean

	let dialog; // HTMLDialogElement

	$: if (dialog && visible) dialog.showModal();
	$: if (dialog && !visible) dialog.close();


    function closeByClickingOnTheBackdrop(event)
    {
        var rect = dialog.getBoundingClientRect()
        var isInDialog = (
            rect.top <= event.clientY && 
            event.clientY <= rect.top + rect.height &&
            rect.left <= event.clientX && 
            event.clientX <= rect.left + rect.width
        )
        if (!isInDialog) {
            dialog.close()
        }
    }

</script>

<!-- svelte-ignore a11y-click-events-have-key-events a11y-no-noninteractive-element-interactions -->
<dialog
    style="width:{width};height:{height};"
	bind:this={dialog}
	on:close={() => (visible = false)}
	on:click|self={closeByClickingOnTheBackdrop}
>
	<!-- svelte-ignore a11y-no-static-element-interactions -->
	<div on:click|stopPropagation>
        <div class="header">
            <slot name="header" />
            <span class="close-button" autofocus on:click={() => dialog.close()}>X</span>
        </div>
        <div class="body">
            <slot />
        </div>
		<!-- svelte-ignore a11y-autofocus -->
		
	</div>
</dialog>

<style>
    .header{
        display: flex;
        background-color: rgba(255, 255, 255, 0.12);
        height: 30px;
        align-items: center;
        padding: 10px;
    }
    .body{
        padding: 10px;   
    }
    .close-button{
        position: absolute;
        right: 0;
        padding: 5px 10px;
        cursor: pointer;
        user-select: none;
    }
    .close-button:hover{
        background-color: var(--secondary);
    }
	dialog {
		border-radius: 0.2em;
		border: none;
		padding: 0;
        background-color: var(--highlight);
        color: white;
	}
	dialog::backdrop {
		background: rgba(0, 0, 0, 0.3);
	}

	dialog[open] {
		animation: zoom 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
	}
	@keyframes zoom {
		from {
			transform: scale(0.95);
		}
		to {
			transform: scale(1);
		}
	}
	dialog[open]::backdrop {
		animation: fade 0.2s ease-out;
	}
	@keyframes fade {
		from {
			opacity: 0;
		}
		to {
			opacity: 1;
		}
	}

</style>