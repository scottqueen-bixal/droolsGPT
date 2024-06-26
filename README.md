# About

This is a forked [example](link) that combines OpenAI and the Drools rules engine to prototype leveraging LLM systems with ChatGPT like interactions which are focused on providing benefits eligibility results to users.

# Resources
- A short list of [requirements](./REQUIREMENTS.md) consideration for future implementation.

# Example

"Likely eligible"


> How can I help you today? > Am I eligible for COVID benefits?

> Can you provide me your name and details about your specific situation.

> My name is Jon Doe and I am 42 years old citizen of the US. My wife, who worked as a doctor, died while visiting her mother in Texas from COVID during the second year of the pandemic. I paid for the funeral expenses, but never filed for reimbursement.

Extracted Applicant

```
 Applicant {
    applicantFirstName = "Jon",
    applicantLastName = "Doe",
    applicantBirthDate = "1981-12-31",
    applicantRelationshipToDeceased = "Spouse",
    applicantMaritalStatus = "Widowed",
    applicantCitizenStatus = true,
    applicantPaidFuneralExpenses = true,
    applicantReimbursedFuneralExpenses = false,
    deceasedDateOfDeath = "2023-12-31",
    deceasedDiedInUs = true,
    deceasedDiedFromCovid = true
  }
 ```

> Yes, Jon Doe is likely eligible for Covid funeral assistance


"Not eligible"

> My name is Jon Doe and I was born in Sudan, but I have a dual citizenship including U.S. I turned 72 yesterday. My wife died while visiting her mother in Texas from COVID during the first year of the pandemic. I paid for the funeral expenses and was reimbursed.

Extracted Applicant

```
 Applicant {
  applicantFirstName = "Jon",
  applicantLastName = "Doe",
  applicantBirthDate = "1951-04-08",
  applicantRelationshipToDeceased = "Spouse",
  applicantMaritalStatus = "Widowed",
  applicantCitizenStatus = true,
  applicantPaidFuneralExpenses = true,
  applicantReimbursedFuneralExpenses = true,
  deceasedDateOfDeath = "2020-04-15",
  deceasedDiedInUs = true,
  deceasedDiedFromCovid = true
}
```

> Jon Doe is not eligible for Covid funeral assistance because [The deceased died before May 20th, 2020., The applicant paid for funeral expenses, but already got reimbursed.]


## Original Post

### Power is nothing without control

What you can do nowadays with LLM systems like ChatGPT is simply mind-blowing. I must admit that I cannot stop being surprised and from time to time literally jumping from my seat thinking: "I didn't imagine that AI could do ALSO this!". What is a bit misleading here is that what we now call and tend to identify with Artificial Intelligence is actually Deep Learning which is only a subset of all AI technologies available.

![](images/AI-ML-DL-1.png)

In other words Deep Learning is only a fraction of the whole AI-story. Moreover there are many situations where being surprised is the last thing that you may want. You don't want to jump from your seat when your bank refuses to concede you a mortgage without any human understandable or trackable reason, but only because an LLM said no. And even the bank may want to grant their mortgages only to applicants who are considered viable under their strict, well-defined and not questionable business rules.

In essence, the power and flexibility that a well-trained Deep Learning model gives you is virtually infinite, but often, at least in some parts of your application, what you need is more confined control to make it adhere to your business domain and rules.

#### Combining LLM flexibility and rule engine predictability

Given these premises why not mixing 2 very different and complementary AI branches like deep learning and symbolic reasoning? Moving forward with the mortgage example, this will give us a chance to implement an application with the corporate rigor required by the strict business rules of a bank, but queryable in the most human friendly possible way.

In this very straightforward sample project, this has been achieved using a rule engine like [Drools](https://www.drools.org/) to encode the [simple business rules](https://github.com/mariofusco/droolsGPT/blob/main/src/main/resources/mortgage.drl) used by our imaginary bank to evaluate mortgage applications. Through the abstraction layer offered by [langchain4j](https://github.com/langchain4j/langchain4j) API, this has been combined with ChatGPT in order to allow the interaction with our cold and inflexible business rules in the smoothest  and less structured possible way.

#### Running the project

If you want to give a run on your own to this project you need to pass to the Main class your [ChatGPT API key](https://platform.openai.com/account/api-keys).

When the prompt appears you can first try to describe the applicant very informally, something like

```
> Mario, the firstborn of the Fusco family is born on the day of the attack to the twin towers. Nowadays he works as software engineer and earns a quarter million a year.
```

This prompt is forwarded to ChatGPT and transformed by langchain4j into an instance of the [Person class](https://github.com/mariofusco/droolsGPT/blob/main/src/main/java/org/mfusco/Person.java) that is part of the bank business domain and the used by Drools to perform the business rules evaluation.

```
Person { firstName = "Mario", lastName = "Fusco", birthDate = 2001-09-11, income = 250000 }
```

Now we can ask if Mario can obtain a mortgage from our bank

```
> Can we grant a mortgage for Mario?
```

As anticipated this time we don't want to rely on ChatGPT amazing but fuzzy capabilities, but simply apply the bank's business rules through Drools. To do so it is necessary to register it, and in particular the method evaluating the person described above against those rules, [as a tool](https://github.com/mariofusco/droolsGPT/blob/929f69bc369374886907281d8147d0dab4bd6fab/src/main/java/org/mfusco/DroolsMortgageCalculator.java#L25) that ChatGPT has to use to reply to this request. And since Mario's profile fulfills all the business rules requirements the answer will be something like

```
Yes, a mortgage can be granted for Mario.
```

We can try the same with Mario's daughter Sofia and to make things even more challenging we can also attempt to do so in Italian

```
> Sofia, la figlia di Mario Fusco è nata il ventiseisimo giorno del nono mese del 2011. E' una studente molto preparata.
```

Here we said that "Sofia, the daughter of Mario Fusco, is born on the twentysixth day of the ninth month of 2011. She is a very smart student." and still ChatGPT is able to translate this description into another instance of the Person class

```
Person { firstName = "Sofia", lastName = "Fusco", birthDate = 2011-09-26, income = 0 }
```

Of course this time asking if Sofia can also get a mortgage

```
> Possiamo dare un mutuo a Sofia?
```

we will receive a negative answer as expected

```
No, a mortgage cannot be granted for Sofia. This is because Sofia is too young and her income is too low.
```
