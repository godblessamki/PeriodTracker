# Partner Period & Care Tracker

A companion app designed to connect couples by allowing the female partner to share her menstrual cycle data with her partner. The app translates complex hormonal phases into digestible, actionable care tips for the partner, eliminating guesswork and fostering empathy.

## Philosophy
- **Consent-First**: Data sharing is strictly opt-in and easily revocable.
- **Anti-Monolith**: Customizable symptoms and tips, acknowledging individual experiences.
- **Educational**: Grounded in biological facts regarding the four cycle phases (Menstrual, Follicular, Ovulation, Luteal).

## Technology Stack
- **Frontend Mobile**: React Native (Expo), TypeScript, React Navigation, Zustand, React Query
- **Backend API**: ASP.NET Core 8/9 Web API, Entity Framework Core
- **Backend Orchestration**: .NET Aspire
- **Database**: PostgreSQL
- **Push Notifications**: Firebase Cloud Messaging (FCM) / Apple Push Notification service (APNs)

## Architecture Overview
1. **Cycle Tracking Engine**: Algorithmically calculates phase lengths based on start date and cycle length.
2. **Cheat Sheet & Dashboards**: Dedicated UI tailored to the current phase, featuring actionable tips (e.g., heating pad, specific snacks, supportive boundaries).
3. **Secure Pairing**: 6-digit expiring code securely links the Tracker and the Partner accounts, with instant revocation capabilities.

## Getting Started
*(Instructions will be added as implementation phases progress)*

Please see [`plan.md`](./plan.md) for the detailed Project Master Plan.

## Acknowledgments
Built with ❤️ alongside my friend [@adamstefanik](https://github.com/adamstefanik).

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
