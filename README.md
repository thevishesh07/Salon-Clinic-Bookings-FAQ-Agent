Salon & Clinic Booking Assistant (AI Agent)
The problem

Salons and clinics handle bookings, pricing questions and FAQs manually, often while mid-service with another customer. A missed message means a lost booking, and staff repeat the same price/hours questions dozens of times a week.

How it works

Unlike my earlier single-prompt builds, this uses an AI Agent architecture: the model is given a set of tools and decides which to call based on what the customer actually asks, rather than following one fixed path.

get_services / get_faq — pull live prices, durations and policies from Google Sheets, so the bot never answers from memory or invents a price
log_feedback — saves reviews, complaints and suggestions with an auto-classified type
get_bookings / book_slot — check availability and create bookings
Simple Memory — keeps each conversation coherent across multiple messages, keyed per customer
What it handles
Service, price and FAQ questions, grounded only in the business's real data
New bookings, including rejecting clashing, out-of-hours or past-date requests
Cancellation and rescheduling by matching phone number to booking
Customer feedback, logged and categorised automatically
Hindi and Hinglish input
Refuses to share another customer's booking details
Resists attempts to override its instructions via prompt injection
A real bug I caught and fixed

The first version checked slot availability and wrote the new booking as two separate steps, with the AI model reasoning in between. Two customers booking the same slot at nearly the same moment could both pass the availability check before either one's booking was actually written — a classic check-then-write race condition.

Fix: moved the check and the write into a single sub-workflow (Book Slot) that runs both back-to-back with no AI step in between, closing the gap to the point where it's no longer practically reachable at a real salon's message volume.

Known limitation, disclosed rather than hidden: Google Sheets still isn't a transactional database. A business expecting heavy simultaneous booking traffic would eventually need a real database with proper locking — fine for the scale this is built for, worth flagging honestly to a client at higher volume.

Safety rules built in
Prices and policies only ever come from the Sheets tools, never the model's own knowledge
No customer's booking details are shared with another customer
Off-topic requests and instruction-override attempts are declined
The bot never claims a booking or feedback entry is saved until the write actually succeeds
Tech stack

n8n (AI Agent, Simple Memory, Google Sheets Tool nodes), Google Gemini API, Google Sheets (Services, FAQ, Feedback, Bookings)

Status

Built and tested on n8n's Chat Trigger. WhatsApp Business Cloud integration is in progress — connecting the same agent to a real WhatsApp number is the remaining step before client deployment.
