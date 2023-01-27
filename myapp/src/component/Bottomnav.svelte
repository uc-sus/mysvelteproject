<script>
	import { count,choose_answer } from '../store';
	import { onMount } from 'svelte';
	import { tweened } from 'svelte/motion';
	import Sidebar from './Sidebar.svelte';
	// fetch json data
	let dialog;
	let questionArray = [];
	onMount(async () => {
		// localStorage.clear()
		console.log(JSON.parse(localStorage.getItem('user_ans')));
		const res = await fetch('lib/question.json');
		questionArray = await res.json();
		$choose_answer = checked__opt;
    console.log(JSON.parse(questionArray[0]['content_text']));
		dialog = document.getElementById('confirmation-dialog');
	});
	function quesDrop(event) {
		countValue = event.detail.data;
	}
	let checked__opt = [];
	$: console.log($choose_answer);
  // Show the dialog when clicking "Delete everything"
	const showDialogClick = (asModal = true) => {
		try {
			dialog[asModal ? 'showModal' : 'show']();
		} catch (e) {}
	};
	const closeClick = () => {
		dialog.close();
	};
	// timer
	let original = 5 * 60;
	let timer = tweened(original);
	setInterval(() => {
		if ($timer > 0) $timer--;
	}, 1000);
	$: minutes = Math.floor($timer / 60);
	$: seconds = Math.floor($timer - minutes * 60);

	$: sidebar_show = false;

	// counter
	function incrementCounter() {
		count.update((n) => n + 1);
	}
	function decrementCounter() {
		count.update((n) => n - 1);
	}
	let countValue;
	count.subscribe((value) => {
		countValue = value;
		if (countValue >= 11) {
			countValue = 11;
		} else if (countValue <= 1) {
			countValue = 1;
		}
	});
  const checkQuesAvaibility = (ques, ans, data) => {
		let store_ans = true;
		let new_data = JSON.parse(localStorage.getItem('user_ans'));
		if (!new_data) {
			localStorage.setItem('user_ans', JSON.stringify(data));
			return;
		}
		// console.log('new_data', new_data);
		new_data.forEach((item, i) => {
			if (item.que == ques) {
				store_ans = false;
				new_data[i]['ans'] = ans;
				// console.log('nwwwww');
				localStorage.setItem('user_ans', JSON.stringify(new_data));
				return false;
			}
		});
		// console.log('store_ans', store_ans);
		if (store_ans) {
			// console.log('YESSSSSSSS');
			let store_data = [...new_data, data[0]];
			localStorage.setItem('user_ans', JSON.stringify(store_data));
		}
	};

	const saveAnswer = (ques, ans) => {
		let data = [
			{
				que: ques,
				ans: ans
			}
		];
		checkQuesAvaibility(ques, ans, data);
	};
</script>
<main>
	<!-- ...................................................... -->
	<nav class="d-flex mt-1 border-primary bg-light  position-absolute bottom-0 end-0 mr-5">
		<button class="btn m-2">0{minutes}:{seconds}</button>
		<Sidebar on:displaySameQues={quesDrop} bind:show={sidebar_show} />
		<button class="btn btn-dark m-2 px-3" on:click={() => (sidebar_show = !sidebar_show)}
			>List</button
		>
		<!-- <a href="/Sidebar"></a> -->
		<button
			class="btn btn-dark m-2 px-3"
			on:click={decrementCounter}
			on:click={() => (sidebar_show = false)}
			disabled={countValue <= 1 ? true : false}>Previous</button
		>
		<button class="btn px-2">{countValue} of 11</button>
		<button
			class="btn btn-dark m-2 px-3"
			on:click={incrementCounter}
			on:click={() => (sidebar_show = false)}
			disabled={countValue >= 11 ? true : false}>Next</button
		>
		<dialog id="confirmation-dialog" class="height100 border-0 pt-5" style="height:200px;">
			<h3>Are you sure want to end the test?</h3>
			<div class=" mx-auto mt-3" style="width:200px; text-align:center">
				<button class=" btn btn-dark  px-4" on:click={closeClick}>No</button>
				<a href="/result">
					<button class=" btn btn-dark px-4 ">Yes</button>
				</a>
			</div>
		</dialog>
		<button
			class="btn btn-dark m-2 px-3"
			on:click={() => showDialogClick(true)}
			on:click={() => (sidebar_show = false)}>End Test</button
		>
	</nav>
	<section>
		{#each questionArray as data, index}
			{#if index == countValue - 1}
				<div class="mx-5 mt-5 text-bold ">
					{countValue}. {JSON.parse(data.content_text).question}
				</div>
				{#each JSON.parse(data.content_text).answers as answers, j}
					<div class=" ml-5 mt-1">
						<input
						    type="radio"
							on:click={() => saveAnswer(index + 1, j + 1)}
							value={answers.answer}
							name="answer"
							class="ms-5 mt-3"
							id ="check"
							radio="radio{j}"
							bind:group={checked__opt[index]}
						/><span class="ms-3">{@html answers.answer}</span>
					</div>
				{/each}
			{/if}
		{/each}
	</section>
</main>
