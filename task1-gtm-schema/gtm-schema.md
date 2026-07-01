# OrthoNow GTM Event Schema

## Events

| Event Name | Trigger Type | Key Parameters | GA4 Report / Audience |
|-------------|-------------|-------------|-------------|
| booking_step_complete | Custom Event | step_number, clinic_location, specialty | Funnel Exploration |
| booking_completed | Form Success | booking_id, clinic_location, specialty | Conversion Tracking |
| call_now_clicked | Click Trigger | page_name, clinic_name, phone_number | Call Performance Report |
| whatsapp_clicked | Link Click | page_name, device_type, campaign_source | WhatsApp Remarketing Audience |
| patient_guide_downloaded | Form Success | guide_name, user_phone, city | Lead Generation Report |
| clinic_page_viewed | Page View | clinic_name, city, page_url | Location Performance Dashboard |
| blog_scroll_50 | Scroll Depth Trigger | article_name, category, scroll_percent | Content Engagement Report |
| blog_scroll_90 | Scroll Depth Trigger | article_name, category, scroll_percent | High Intent Readers Audience |



## Booking Funnel Tracking

The appointment booking process consists of three steps:

### Step 1
Select Clinic Location and Specialty.

### Step 2
Enter Name, Phone Number, and Preferred Date.

### Step 3
Confirm Booking.



## Step 1 - dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "Bengaluru",
  "specialty": "Knee Pain"
}
```

## Step 2 - dataLayer Push

```json
{
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "patient_name": "Rohit Kumar",
  "phone_number": "9876543210",
  "preferred_date": "2026-07-10"
}
```

## Step 3 - dataLayer Push

```json
{
  "event": "booking_completed",
  "step_number": 3,
  "booking_id": "BK1001",
  "clinic_location": "Bengaluru",
  "specialty": "Knee Pain"
}
```



## Funnel Drop-off Tracking in GA4

GTM cannot automatically detect multi-step form progression.

The front-end developer will implement the `window.dataLayer.push()` events at each booking step. Google Tag Manager will listen to these custom events and forward them to GA4.

The GTM trigger configuration would be:

- Step 1 Trigger: Custom Event = `booking_step_complete`, filter `step_number = 1`
- Step 2 Trigger: Custom Event = `booking_step_complete`, filter `step_number = 2`
- Step 3 Trigger: Custom Event = `booking_completed`

GA4 Funnel Exploration will use the following sequence:

1. `booking_step_complete` (step_number = 1)
2. `booking_step_complete` (step_number = 2)
3. `booking_completed`

This setup enables visibility into user drop-off between each booking stage and helps optimize the consultation booking experience.



## Google Ads Conversion Action

Selected Conversion Event: `booking_completed`

### Reason

The `booking_completed` event represents an actual appointment request and has the highest business value.

Unlike call clicks or WhatsApp clicks, it confirms that the patient completed the entire booking journey. Therefore, it should be imported into Google Ads as the primary conversion action for campaign optimization.
