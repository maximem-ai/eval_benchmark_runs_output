# LongMemEval_S: questions with dataset defects

Three LongMemEval_S questions have gold answers that the conversations do not support. Our run answered all three wrong. Excluding only these three, the LongMemEval score on answerable questions is **449 / 467 = 96.1%**.

This file explains each one. The IDs are also in [`dataset_defects.json`](dataset_defects.json).

## How we decided

- A question is listed when its gold answer cannot be derived from the conversations without an assumption the user never stated.
- Other questions we found ambiguous are not excluded.
- Quotes below are verbatim from the public LongMemEval_S conversations. Session dates are the dates in that data.

## 1. `37f165cf` (multi-session)

**Question:** What was the page count of the two novels I finished in January and March?
**Gold:** 856 (that is 440 + 416)
**Our answer:** CANNOT_ANSWER

No user turn in the conversations ties any novel to January or March. The two page counts come from sessions dated in May, and both books are described as just finished:

- Session `answer_6b9b2b1e_2`, 2023/05/22: "I just finished a historical fiction novel, "The Nightingale" by Kristin Hannah, which had 440 pages and took me around 3 weeks to complete."
- Session `answer_6b9b2b1e_1`, 2023/05/27: "I just finished a 416-page novel, but before that, I read "The Power" by Naomi Alderman in December, which had 341 pages and took me around 5 weeks to finish."

The gold only holds if you assume which two books are the January and March ones. Status: **confirmed**.

## 2. `gpt4_731e37d7` (multi-session)

**Question:** How much total money did I spend on attending workshops in the last four months?
**Gold:** $720
**Our answer:** $220

The question is asked on 2023/02/26. The gold is the sum of three workshop costs:

- Session `answer_826d51da_4`: "a writing workshop I attended in November at a literary festival. It was a two-day workshop, and I paid $200 to attend." ($200)
- Session `answer_826d51da_3`: "a half-day mindfulness workshop at a yoga studio near my home on December 12 ... I paid $20 to attend" ($20)
- Session `answer_826d51da_2`, dated 2023/02/26: "I just attended a digital marketing workshop at the city convention center on March 15-16 ... I paid $500 to attend" ($500)

The $500 workshop is said to be "just attended" in a session dated February 26, but its stated date is March 15-16. That date is in the future if it is March 2023, and 11 months back if it is March 2022. Either way it is outside "the last four months". Read literally, the answer is $200 + $20 = $220. The gold of $720 only holds by overriding the stated date. Status: **confirmed**.

## 3. `7405e8b1` (multi-session)

**Question:** Did I receive a higher percentage discount on my first order from HelloFresh, compared to my first UberEats order?
**Gold:** Yes.
**Our answer:** CANNOT_ANSWER

- Session `answer_80323f3f_1`, 2023/05/23: "I recently tried HelloFresh and got a 40% discount on my first order, which was a great deal!"
- Session `answer_80323f3f_2`, 2023/05/28: "I'm planning to order food from UberEats again this week ... By the way, last week I got 20% off my UberEats order, which was awesome!"
- Session `answer_80323f3f_1`, 2023/05/23: "I've been using these services a lot lately, especially UberEats."

HelloFresh's 40% is stated as a first order. For UberEats the user only says they got 20% off "last week", and describes ordering from it often. Nothing says that order was the first one. The gold "Yes" assumes it was. Status: **arguable**. A reader could consider that assumption reasonable.

## Reviewed and kept as our miss: `gpt4_f420262d`

**Question:** What was the airline that I flied with on Valentine's day?
**Gold:** American Airlines
**Our answer:** JetBlue

This was flagged as a possible defect, and we checked it. "Valentine's" never appears in the conversations, and the American Airlines flight is dated three different ways across sessions (February 10th, "today" on February 20, and a LAX to JFK flight). But in the session dated 2023/02/14, the only flight the user says they have flown is American Airlines:

- Session `answer_d8a1af6c_5`, 2023/02/14: "I'm still recovering from my American Airlines flight from LAX to JFK, which was delayed by 2 hours due to bad weather conditions."

In the same session the user books a new flight: "I think I'll take the JetBlue option from BOS to FLL." That is a booking for a future trip, not a flight flown that day. Every version of the dates still points to American Airlines, so the gold holds and our JetBlue answer is a miss. It stays in the score.

## Numbers

| Set | Correct / total | Score |
|---|---|---|
| All 500 questions | 476 / 500 | 95.2% |
| Answerable (30 abstention questions excluded) | 449 / 470 | 95.5% |
| Answerable, excluding the 3 defect questions above | 449 / 467 | 96.1% |

To reproduce the third number, see the commands in the repository [README](../../README.md#check-the-longmemeval-number-yourself).
