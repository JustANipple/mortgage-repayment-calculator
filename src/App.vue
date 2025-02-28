<script setup>
import { ref } from "vue";
import BaseInput from "./components/BaseInput.vue";
import BaseRadioGroup from "./components/BaseRadioGroup.vue";

const mortgage = ref({
    amount: "",
    term: "",
    rate: "",
    type: "",
});

const options = ref([
    { label: "Repayment", value: "repayment" },
    { label: "Interest Only", value: "interest-only" },
]);

const showResult = ref(false);
const monthlyRepayment = ref(0.0);
const totalRepayment = ref(0.0);

const errors = ref({
    amount: "",
    term: "",
    rate: "",
    type: "",
});

// Calculate mortgage repayment
const calculateRepayment = () => {
    const { amount, term, rate, type } = mortgage.value;

    // Convert values to numbers
    const principal = parseFloat(amount);
    const years = parseFloat(term);
    const annualRate = parseFloat(rate);

    if (isNaN(principal) || isNaN(years) || isNaN(annualRate)) {
        return;
    }

    const monthlyRate = annualRate / 100 / 12;
    const numberOfPayments = years * 12;

    if (type === "repayment") {
        const monthlyRepayment =
            (principal *
                monthlyRate *
                Math.pow(1 + monthlyRate, numberOfPayments)) /
            (Math.pow(1 + monthlyRate, numberOfPayments) - 1);
        return monthlyRepayment.toFixed(2);
    } else if (type === "interest-only") {
        const monthlyInterestOnly = principal * monthlyRate;
        return monthlyInterestOnly.toFixed(2);
    }
};

const sendForm = () => {
    errors.value = { amount: "", term: "", rate: "", type: "" };

    const { amount, term, rate, type } = mortgage.value;

    if (
        !amount ||
        isNaN(parseFloat(amount)) ||
        parseFloat(amount) <= 0
    ) {
        errors.value.amount = "This field is required";
    }
    if (!term || isNaN(parseFloat(term)) || parseFloat(term) <= 0) {
        errors.value.term = "This field is required";
    }
    if (!rate || isNaN(parseFloat(rate)) || parseFloat(rate) <= 0) {
        errors.value.rate = "This field is required";
    }
    if (!type) {
        errors.value.type = "This field is required";
    }

    if (Object.values(errors.value).some((err) => err !== "")) {
        return;
    }

    monthlyRepayment.value = calculateRepayment();
    totalRepayment.value = (
        monthlyRepayment.value *
        mortgage.value.term *
        12
    ).toFixed(2);
    showResult.value = true;
};

function clearForm() {
    mortgage.value = {
        amount: "",
        term: "",
        rate: "",
        type: "",
    };
    showResult.value = false;
}
</script>

<template>
    <main>
        <div class="main">
            <h1 class="title">Mortgage Calculator</h1>
            <button class="clear" @click="clearForm">
                Clear All
            </button>

            <form @submit.prevent="sendForm" class="form">
                <BaseInput
                    :label="'Mortgage Amount'"
                    :text-content="'£'"
                    :isLeft="true"
                    :error="errors.amount"
                    v-model="mortgage.amount"
                    type="number"
                    class="amount"
                />

                <BaseInput
                    :label="'Mortgage Term'"
                    :text-content="'years'"
                    :isLeft="false"
                    :error="errors.term"
                    v-model="mortgage.term"
                    type="number"
                    class="term"
                />

                <BaseInput
                    :label="'Interest Rate'"
                    :text-content="'%'"
                    :isLeft="false"
                    :error="errors.rate"
                    v-model="mortgage.rate"
                    type="number"
                    class="rate"
                />

                <div class="radioGroup">
                    <h3 class="radioLabel">Mortgage Type</h3>
                    <BaseRadioGroup
                        :options="options"
                        :name="'type'"
                        :vertical="true"
                        :error="errors.type"
                        v-model="mortgage.type"
                    />
                </div>

                <button type="submit" class="calculateButton">
                    <img
                        src="../public/icon-calculator.svg"
                        alt="calculator"
                    />
                    <span> Calculate Repayments </span>
                </button>
            </form>
        </div>
        <footer class="footer">
            <div v-show="!showResult" class="resultPlaceholder">
                <img
                    src="/illustration-empty.svg"
                    alt="illustration"
                />
                <div class="placeholderText">
                    <p class="placeholderTitle">Results shown here</p>
                    <p class="placeholderParagraph">
                        Complete the form and click “calculate
                        repayments” to see what your monthly
                        repayments would be.
                    </p>
                </div>
            </div>
            <div v-show="showResult" class="resultContainer">
                <p class="resultTitle">Your results</p>
                <p class="resultParagraph">
                    Your results are shown below based on the
                    information you provided. To adjust the results,
                    edit the form and click “calculate repayments”
                    again.
                </p>
                <div class="result">
                    <p class="monthlyResultText">
                        Your monthly repayments
                    </p>
                    <p class="monthlyResultPrice">
                        £{{ monthlyRepayment }}
                    </p>
                    <div class="divider"></div>
                    <p class="totalResultText">
                        Total you'll repay over the term
                    </p>
                    <p class="totalResultPrice">
                        £{{ totalRepayment }}
                    </p>
                </div>
            </div>
        </footer>
    </main>
</template>

<style scoped>
.main {
    background-color: var(--white);
    padding: 24px;
    padding-bottom: 32px;
}

.title {
    color: var(--slate900);
    font-size: 24px;
    line-height: 200%;
}

.clear {
    background-color: transparent;
    color: var(--slate700);
    text-decoration: underline;
    border: unset;
    padding: unset;
}

.form {
    padding-top: 24px;
    display: grid;
    row-gap: 24px;
}

.radioGroup {
    display: grid;
    row-gap: 10px;
}

.radioLabel {
    color: var(--slate700);
    font-size: 16px;
    font-weight: var(--fw-medium);
}

.calculateButton {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 12px;
    padding-block: 16px;
    border: unset;
    border-radius: 2rem;
    background-color: var(--primary-color);
    font-size: 18px;
    font-weight: var(--fw-bold);
    line-height: 100%;
    color: var(--slate900);
}

.resultPlaceholder {
    display: grid;
    place-items: center;
    row-gap: 16px;
    padding-block: 30px;
    background-color: var(--slate900);
    color: var(--white);
}

.placeholderText {
    text-align: center;
    display: grid;
    row-gap: 12px;
}

.placeholderTitle {
    font-weight: var(--fw-bold);
    font-size: 24px;
}

.placeholderParagraph {
    color: var(--slate300);
}

.resultContainer {
    display: grid;
    row-gap: 18px;
    background-color: var(--slate900);
    padding: 36px 24px;
    color: var(--white);
}

.resultTitle {
    font-weight: var(--fw-bold);
    font-size: 24px;
    line-height: 100%;
}

.resultParagraph {
    color: var(--slate300);
}

.result {
    display: grid;
    row-gap: 12px;
    padding: 24px 16px;
    margin-block-start: 8px;
    -webkit-box-shadow: inset 0px 4px 0px 0px var(--primary-color);
    box-shadow: inset 0px 4px 0px 0px var(--primary-color);
    border-radius: 6px;
    background-color: var(--slate900-dark);
}

.monthlyResultText {
    color: var(--slate300);
}

.monthlyResultPrice {
    color: var(--primary-color);
    font-size: 40px;
    font-weight: var(--fw-bold);
    line-height: 100%;
}

.divider {
    margin-block: 8px;
    border-top: 1px solid var(--slate700);
}

.totalResultText {
    color: var(--slate300);
}

.totalResultPrice {
    font-size: 24px;
    font-weight: var(--fw-bold);
    line-height: 100%;
}

@media screen and (min-width: 1024px) {
    main {
        background-color: var(--slate100);
        display: flex;
        justify-content: center;
        max-width: fit-content;
        background-color: var(--white);
        border-top-left-radius: 1.5rem;
        border-bottom-left-radius: 1.5rem;
        overflow: hidden;
    }

    .main {
        flex: 1;
        max-width: 31.5rem;
        display: grid;
        grid-template-columns: repeat(12, 1fr);
        grid-template-rows: repeat(11, 1fr);
        padding-block: 32px;
        padding-bottom: 42px;
        padding-inline: 42px;
        background-color: var(--white);
    }

    .form {
        gap: 22px;
        grid-column: 1 / 13;
        grid-row: 2 / 12;
    }

    .title {
        grid-column: 1 / 9;
        grid-row: 1 / 2;
    }

    .clear {
        grid-column: 11 / 13;
        grid-row: 1 / 2;
    }

    .amount {
        grid-column: 1 / 12;
        grid-row: 1 / 2;
    }

    .term {
        grid-column: 1 / 6;
        grid-row: 2 / 3;
    }

    .rate {
        grid-column: 7 / 12;
        grid-row: 2 / 3;
    }

    .radioGroup {
        grid-column: 1 / 12;
        grid-row: 3 / 4;
    }

    .calculateButton {
        grid-column: 1 / 9;
        grid-row: 5 / 6;
    }

    .footer {
        flex: 1;
        max-width: 31.5rem;
        place-content: center;
        background-color: var(--slate900);
        border-top-right-radius: 1.5rem;
        border-bottom-right-radius: 1.5rem;
        border-bottom-left-radius: 4rem;
        overflow: hidden;
    }

    .resultPlaceholder {
        padding-inline: 42px;
    }

    .resultContainer {
        padding: 42px 42px;
        row-gap: 24px;
    }

    .result {
        padding-block: 32px;
        padding-inline: 30px;
    }

    .monthlyResultPrice {
        font-size: 58px;
    }

    .divider {
        margin-block: 24px;
    }
}
</style>
