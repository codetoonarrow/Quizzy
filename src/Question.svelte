<script>
    import { score } from './store.js';
    import { fade } from "svelte/transition";
    export let question;
    export let nextQuestion;
    let letters = ["a", "b" , "c", "d"]
    let isCorrect;
    let isAnswered = false;

    let answers = question.incorrect_answers.map(answer => {
        return {
            answer,
            correct: false
        };
    });

    let allAnswers = [
        ...answers,
        {
            answer: question.correct_answer,
            correct: true,
            letters: letters
        }
    ];

    shuffle(allAnswers);

    function shuffle(array) {
        array.sort(() => Math.random() - 0.5)
    }

    function checkQuestion(correct){
        if(!isAnswered){
            isAnswered = true;
            isCorrect = correct;
            if(correct){
                score.update(val => val + 1);
            }
        }
    }
</script>

<h3>{@html question.question}</h3>

{#if isAnswered}
    <h5 class:isCorrect transition:fade class:wrong={!isCorrect}>
    {#if isCorrect}
        You got it right
    {:else}
        You got it wrong
    {/if}     
    </h5>
{/if}

<!-- If the answer is incorrect show the correct answer by higlighting it when a green border -->
{#each allAnswers as answer, i}
    <h4 class:isCorrect={ isCorrect && isAnswered === answer.correct} 
        class:wrong={!isCorrect && isAnswered && !answer.correct}
    >{letters[i]}</h4>
     <button class="answer" disabled={isAnswered} on:click={() => checkQuestion(answer.correct)}>
         {@html answer.answer}
    </button>
{/each}

{#if isAnswered}
    <div>
        <button on:click={nextQuestion}>Next Question</button>
    </div>
{/if}

<style>
    h5.wrong {
        color: red;
    }
    h4.wrong {
        color: red;
    }
    h5.isCorrect{
        color:green;
    }
    h4.isCorrect {
        border: 5px solid green;
        display: inline-block;
    }
    .answer {
        display:inline-block;
        border-radius: 5px;
    }

</style>