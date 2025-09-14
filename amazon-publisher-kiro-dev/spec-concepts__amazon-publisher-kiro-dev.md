## Select your cookie preferences

We use essential cookies and similar tools that are necessary to provide our site and services. We use performance cookies to collect anonymous statistics, so we can understand how customers use our site and make improvements. Essential cookies cannot be deactivated, but you can choose “Customize” or “Decline” to decline performance cookies.

If you agree, AWS and approved third parties will also use cookies to provide useful site features, remember your preferences, and display relevant content, including relevant advertising. To accept or decline all non-essential cookies, choose “Accept” or “Decline.” To make more detailed choices, choose “Customize.”

AcceptDeclineCustomize

## Customize cookie preferences

We use cookies and similar tools (collectively, "cookies") for the following purposes.

### Essential

Essential cookies are necessary to provide our site and services and cannot be deactivated. They are usually set in response to your actions on the site, such as setting your privacy preferences, signing in, or filling in forms.

### Performance

Performance cookies provide anonymous statistics about how customers navigate our site so we can improve site experience and performance. Approved third parties may perform analytics on our behalf, but they cannot use the data for their own purposes.

Allowed

### Functional

Functional cookies help us provide useful site features, remember your preferences, and display relevant content. Approved third parties may set these cookies to provide certain site features. If you do not allow these cookies, then some or all of these services may not function properly.

Allowed

### Advertising

Advertising cookies may be set through our site by us or our advertising partners and help us deliver relevant marketing content. If you do not allow these cookies, you will experience less relevant advertising.

Allowed

Blocking some types of cookies may impact your experience of our sites. You may review and change your choices at any time by selecting Cookie preferences in the footer of this site. We and selected third-parties use cookies or similar technologies as specified in the [AWS Cookie Notice](https://aws.amazon.com/legal/cookies/).

CancelSave preferences

## Unable to save cookie preferences

We will only store essential cookies at this time, because we were unable to save your cookie preferences.

If you want to change your cookie preferences, try again later using the link in the AWS console footer, or contact support if the problem persists.

Dismiss

Documentation

# Concepts

Specs bridge the gap between conceptual product requirements and technical implementation details, ensuring alignment and reducing development iterations. Kiro generates three key files that form the foundation of each specification:

- **requirements.md** \- Captures user stories and acceptance criteria in structured EARS notation
- **design.md** \- Documents technical architecture, sequence diagrams, and implementation considerations
- **tasks.md** \- Provides a detailed implementation plan with discrete, trackable tasks

Idea for feature 'foo'

Open spec session in chat

Click '+' in spec pane

.kiro/specs/foo

requirements.md

design.md

tasks.md

## [Copied!](https://kiro.dev/docs/specs/concepts/\#workflow) Workflow

The workflow follows a logical progression with decision points between phases, ensuring each step is properly completed before moving to the next.

- **Requirements Phase** (leftmost section): Define user stories and acceptance criteria in structured EARS notation
- **Design Phase** (second section): Document the technical architecture, sequence diagrams, and implementation considerations
- **Implementation Planning** (third section): Break down the work into discrete, trackable tasks with clear descriptions and outcomes
- **Execution Phase** (rightmost section): Track progress as tasks are completed, with the ability to update and refine the spec as needed

no

yes

no

yes

Start a spec

requirements.md

Happy?

Edit/Request changes

design.md

Happy?

Edit/Request changes

Implementation

## [Copied!](https://kiro.dev/docs/specs/concepts/\#requirements) Requirements

The `requirements.md` file is written in the form of user stories with acceptance criteria in EARS notation. The way you wish your PM would give you requirements!

EARS (Easy Approach to Requirements Syntax) notation provides a structured format for writing clear, testable requirements. In a spec's requirements.md file, each requirement follows this pattern:

```code-highlight

WHEN [condition/event]
THE SYSTEM SHALL [expected behavior]

```

For example:

```code-highlight

WHEN a user submits a form with invalid data
THE SYSTEM SHALL display validation errors next to the relevant fields

```

This structured approach offers several benefits:

- **Clarity**: Requirements are unambiguous and easy to understand
- **Testability**: Each requirement can be directly translated into test cases
- **Traceability**: Individual requirements can be tracked through implementation
- **Completeness**: The format encourages thinking through all conditions and behaviors

Kiro helps you transform vague feature requests into these well-structured requirements, making the development process more efficient and reducing misunderstandings between product and engineering teams.

## [Copied!](https://kiro.dev/docs/specs/concepts/\#design) Design

![Design documentation in Kiro specs](https://kiro.dev/videos/specs-design.gif)

The `design.md` file is where you document technical architecture, sequence diagrams, and implementation considerations. It's a great place to capture the big picture of how the system will work, including the components and their interactions.

Kiro's specs offer a structured approach to design documentation, making it easier to understand and collaborate on complex systems. The design.md file is a great place to capture the big picture of how the system will work, including the components and their interactions.

design.md

Architecture

Data Flow

Interfaces

Data Models

Error Handling

Unit Testing Strategy

...

## [Copied!](https://kiro.dev/docs/specs/concepts/\#implementation-plan) Implementation plan

The `tasks.md` file is where you provide a detailed implementation plan with discrete, trackable tasks and sub-tasks. Each task is clearly defined, with a clear description, expected outcome, and any necessary resources or dependencies. Kiro's specs offer a structured approach to implementation plans, making it easier to understand and collaborate on complex systems.

Kiro provides a task execution interface for `tasks.md` files that displays real-time status updates. Tasks are updated as in-progress or completed, allowing you to efficiently track implementation progress and maintain an up-to-date view of your development status.

![Design documentation in Kiro specs](https://kiro.dev/videos/spec-task.gif)

Page updated: July 14, 2025

[Specs](https://kiro.dev/docs/specs/)

[Best Practices](https://kiro.dev/docs/specs/best-practices/)

* * *