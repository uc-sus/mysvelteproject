<script>
	import { answerchoosebyuser, actualcorrect } from '../../store';
    import Navbar from '../../component/Nav.svelte';
	import { onMount } from 'svelte';
	let questionArray = [];
	let question_no;
	onMount(async () => {
		console.log('YESS');
		const res = await fetch('lib/question.json');
		questionArray = await res.json();
		question_no = new URL(location.href).searchParams.get('qno');
		console.log(questionArray);
	});
</script>

<!-- reveiw page switch -->
<main>
	<Navbar />
	<section>
		{#each questionArray as data, index}
			{#if question_no == index + 1}
				{#if $answerchoosebyuser[index] == $actualcorrect[index]}
					<div
						class="w-fit-content d-table p-md rounded mx-auto p_review_option alert-success  mt-3 px-3 py-1"
					>
						<!-- <span  class="icomoon-new-24px-correct-1 mr-1"></span> -->
						<h5 class="">Correct</h5>
					</div>
				{:else if $answerchoosebyuser[index] == null}
					<div
						class="w-fit-content d-table p-md rounded mx-auto p_review_option alert-warning  mt-3 px-3 py-1"
					>
						<!-- <span  class="icomoon-new-24px-correct-1 mr-1"></span> -->
						<h4 class="">Unattempted</h4>
					</div>
				{:else}
					<div
						class="w-fit-content d-table p-md rounded mx-auto p_review_option alert-danger  mt-3 px-3 py-1"
					>
						<!-- <span  class="icomoon-new-24px-correct-1 mr-1"></span> -->
						<h5 class="">Incorrect</h5>
					</div>
				{/if}
				<div class="mx-5 mt-2 text-bold ">
					{index + 1}. {JSON.parse(data.content_text).question}
				</div>
				{#each JSON.parse(data.content_text).answers as answers}
					{#if answers.is_correct == 1}
						<div class=" ml-5 mt-1">
							<input type="radio" value=" " name="answer" class="ms-5 mt-3" checked /><span
								class="ms-3">{@html answers.answer}</span
							>
						</div>
					{:else}
						<div class=" ml-5 mt-1">
							<input type="radio" value=" " name="answer" class="ms-5 mt-3" disabled /><span
								class="ms-3">{@html answers.answer}</span
							>
						</div>
					{/if}
				{/each}
				<div class="mx-5 mt-5">
					<h4 class="fw-bold">Explanation:</h4>
					{@html JSON.parse(data.content_text).explanation}
				</div>
			{/if}
		{/each}
	</section>
</main>

<style>
	:global(seq::before) {
		content: attr(no);
		text-transform: uppercase;
	}
</style>
