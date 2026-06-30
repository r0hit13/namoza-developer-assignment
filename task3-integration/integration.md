# OrthoNow Integration Design

## End-to-End Architecture

The landing page will submit consultation data to a lightweight backend API built using Node.js and Express. This backend will act as the integration layer between the landing page, HubSpot CRM, Karix WhatsApp Business API, and Google Ads conversion tracking.

When a patient submits the form, the backend will first validate the request and extract the following information: Name, Phone Number, Clinic Preference, Source, and Lead Status. The clinic preference can be passed through a hidden form field or a clinic-specific landing page configuration.

I would use the HubSpot CRM Contacts API through a direct server-to-server API call rather than Zapier, Make, or an embedded HubSpot form. A direct API integration provides lower latency, greater reliability, better error handling, and full control over healthcare-specific business logic.

A critical consideration is that HubSpot performs deduplication based on email address by default, not phone number. Since this landing page collects only name and phone number, the backend must implement custom phone-based deduplication. The system will first search for an existing contact using the phone number. If a matching record exists, it will be updated; otherwise, a new contact will be created.

After the HubSpot operation succeeds, the backend will trigger a confirmation message using Namoza's Karix WhatsApp Business API integration. The patient should receive this message within two minutes of form submission.

Finally, the `consultation_form_submitted` conversion event will be sent to Google Ads through Google Tag Manager and GA4 conversion imports so that campaigns can optimize toward completed consultation enquiries.

## Biggest Failure Point and Fallback Strategy

The biggest failure point in this architecture is the HubSpot API becoming unavailable or returning an error. If contact creation fails, valuable patient enquiries could be lost.

To mitigate this risk, every form submission should first be written to a backup database or message queue before any external API calls are made. Failed requests can then be retried automatically, ensuring that no leads are permanently lost.

## WhatsApp SLA Monitoring

The two-minute WhatsApp SLA could be affected by Karix API downtime, network latency, queue congestion, or backend processing failures.

To monitor this requirement, the system should maintain timestamps for form submission, HubSpot completion, and WhatsApp delivery. Automated alerts should trigger whenever delivery time exceeds two minutes. A monitoring dashboard showing average response times, failed messages, and retry counts would help maintain operational reliability.