<script lang="ts">
  import { animate, spring } from '@motionone/dom';

  let inputs = Array(6).fill('');
  let inputRefs = Array(6);
  let isUnlocked = false;
  let isError = false;
  $: digitsLeft = 6 - inputs.filter(Boolean).length;
  $: isComplete = inputs.every(input => input !== '');
  $: if (!isComplete) {
    isUnlocked = false;
    isError = false;
  }
  $: if (isComplete) handleSubmit();

  const handleInput = (index, event) => {
    const value = event.target.value;
    
    if (!/^\d*$/.test(value)) {
      event.target.value = inputs[index];
      return;
    }

    inputs[index] = value.slice(-1);
    
    if (value && index < 5) {
      inputRefs[index + 1].focus();
    }
    
    inputs = [...inputs]; 
  }

  const handleKeydown = (index, event) => {
    if (event.key === 'Backspace' && !inputs[index] && index > 0) {
      inputs[index - 1] = '';
      inputRefs[index - 1].focus();
      inputs = [...inputs];
    }
    
    if (event.key === 'ArrowLeft' && index > 0) {
      inputRefs[index - 1].focus();
    }
    
    if (event.key === 'ArrowRight' && index < 5) {
      inputRefs[index + 1].focus();
    }
  }

  const handlePaste = (event) => {
    event.preventDefault();
    const pastedData = event.clipboardData.getData('text');
    const numbers = pastedData.match(/\d/g);
    
    if (numbers) {
      inputs = Array(6).fill('');
      numbers.slice(0, 6).forEach((num, index) => {
        inputs[index] = num;
      });
      inputs = [...inputs];
      
      const nextEmptyIndex = inputs.findIndex(val => !val);
      if (nextEmptyIndex !== -1) {
        inputRefs[nextEmptyIndex].focus();
      } else {
        inputRefs[5].focus();
      }
    }
  }

  const successAnimation = (node) => {
    let animation: any;

    const animateSuccess = () => {
      animation = animate(
        node,
        { 
          scale: [1, 1.2, 1],
          rotate: [0, -10, 10, -10, 0],
        },
        { 
          duration: 0.6,
          easing: spring({ stiffness: 200, damping: 10 })
        }
      );
    };

    animateSuccess();

    return {
      update: animateSuccess,
      destroy: () => animation?.stop()
    };
  };

  const successBackgroundAnimation = (node) => {
    let animation: any;

    const animateBackground = () => {
      if (!isUnlocked) return;
      
      animation = animate(
        node,
        { 
          scale: [1, 1.1, 1],
          opacity: [0.8, 1]
        },
        { 
          duration: 0.5,
          easing: "ease-out"
        }
      );
    };

    animateBackground();

    return {
      update: animateBackground,
      destroy: () => animation?.stop()
    };
  };

  const successTextAnimation = (node) => {
    let animation: any;

    const animateText = () => {
      animation = animate(
        node,
        { 
          y: [20, 0],
          opacity: [0, 1],
          scale: [0.9, 1]
        },
        { 
          duration: 0.4,
          easing: spring({ stiffness: 100, damping: 15 })
        }
      );
    };

    animateText();

    return {
      update: animateText,
      destroy: () => animation?.stop()
    };
  };


  const slideAnimation = (node) => {
    let animation: any;

    const slide = () => {
      animation = animate(
        node,
        { 
          opacity: [0, 1],
          x: [-20, 0]
        },
        { 
          duration: 0.3,
          easing: "ease-out"
        }
      );
    };

    slide();

    return {
      update: slide,
      destroy: () => animation?.stop()
    };
  };

  const iconAnimation = (node) => {
    let animation: any;

    const animateIcon = () => {
      animation = animate(
        node,
        { 
          scale: [0.8, 1],
          opacity: [0, 1]
        },
        { 
          duration: 0.3,
          easing: "ease-out"
        }
      );
    };

    animateIcon();

    return {
      update: animateIcon,
      destroy: () => animation?.stop()
    };
  };

  const handleSubmit = () => {
    const code = inputs.join('');
    if (code.length === 6) {
      if (code === '123456') {
        isUnlocked = true;
        isError = false;
      } else {
        isError = true;
        isUnlocked = false;
        document.querySelectorAll('.input-wrapper').forEach(wrapper => {
          animate(
            wrapper,
            { x: [0, -4, 4, -4, 4, 0] },
            { duration: 0.5, easing: "ease-in-out" }
          );
        });
      }
      console.log('test===>', code);
    }
  }
</script>

<style>
  .input-wrapper {
    position: relative;
    border-radius: 0.5rem;
    overflow: hidden;
  }

  .input-wrapper::before {
    content: '';
    position: absolute;
    left: 0;
    bottom: 0;
    width: 0;
    height: 2px;
    background: #3B82F6;
    transition: width 0.3s ease;
    z-index: 1;
  }

  .input-wrapper:focus-within::before {
    width: 100%;
  }

  :global(.auth-input) {
    @apply w-12 h-16 text-3xl text-center border-2 border-gray-200 rounded-lg 
           focus:border-blue-500 focus:ring-2 focus:ring-blue-200 focus:outline-none
           transition-all duration-200 bg-white relative;
  }

  :global(.auth-input.error) {
    @apply border-red-500 focus:border-red-500 focus:ring-red-200 text-red-500;
  }
</style>

<div class="flex flex-col items-center justify-center min-h-screen bg-gray-50 p-4">
  <div class="w-full max-w-md p-8 bg-white rounded-2xl shadow-lg">
    <div class="flex justify-center mb-6">
      {#if isUnlocked}
        <div class="w-20 h-20 bg-green-50 rounded-full flex items-center justify-center transition-colors duration-300" use:successBackgroundAnimation>
          <div use:successAnimation>
            <svg class="w-10 h-10 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z" />
            </svg>
          </div>
        </div>
      {:else if isError}
        <div class="w-20 h-20 bg-red-50 rounded-full flex items-center justify-center transition-colors duration-300">
          <div use:iconAnimation>
            <svg class="w-10 h-10 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 11V7a4 4 0 118 0v4M7 11h10a2 2 0 012 2v7a2 2 0 01-2 2H7a2 2 0 01-2-2v-7a2 2 0 012-2z" />
            </svg>
          </div>
        </div>
      {:else if isComplete}
        <div class="w-20 h-20 bg-blue-50 rounded-full flex items-center justify-center transition-colors duration-300">
          <div use:iconAnimation>
            <svg class="w-10 h-10 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 11V7a4 4 0 118 0m-4 8v2m-2-2h4" />
            </svg>
          </div>
        </div>
      {:else}
        <div class="w-20 h-20 bg-blue-50 rounded-full flex items-center justify-center transition-colors duration-300">
          <div use:iconAnimation>
            <svg class="w-10 h-10 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 11V7a4 4 0 118 0v4M7 11h10a2 2 0 012 2v7a2 2 0 01-2 2H7a2 2 0 01-2-2v-7a2 2 0 012-2z" />
            </svg>
          </div>
        </div>
      {/if}
    </div>

    <h1 class="text-3xl font-bold text-center mb-4">Easy peasy</h1>
    
    <p class="text-gray-500 text-center mb-8">
      Enter 6-digit code from your two factor authenticator APP.
    </p>

    <div class="flex justify-between gap-2 mb-8">
      {#each inputs as input, i}
        <div class="input-wrapper">
          <input
            type="text"
            inputmode="numeric"
            maxlength="1"
            bind:this={inputRefs[i]}
            value={input}
            on:input={(e) => handleInput(i, e)}
            on:keydown={(e) => handleKeydown(i, e)}
            on:paste={handlePaste}
            class="auth-input {isError ? 'error' : ''}"
          />
        </div>
      {/each}
    </div>

    <div use:slideAnimation>
      <button
        on:click={handleSubmit}
        class="w-full py-4 {isUnlocked ? 'bg-green-500 hover:bg-green-600' : isError ? 'bg-red-500 hover:bg-red-600' : 'bg-blue-500 hover:bg-blue-600'} text-white rounded-lg text-lg font-semibold transition-all duration-300 relative overflow-hidden"
        class:opacity-50={!isComplete}
        disabled={!isComplete}
      >
        {#if isUnlocked}
          <span use:successTextAnimation>
            Let's go!
          </span>
        {:else}
          <span>
            {#if isError}
              Wrong code!
            {:else}
              {isComplete ? "Let's go!" : `${digitsLeft} ${digitsLeft === 1 ? 'digit' : 'digits'} left`}
            {/if}
          </span>
        {/if}
      </button>
    </div>
  </div>
</div>
