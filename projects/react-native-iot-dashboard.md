---
layout: page
permalink: /projects/react-native-iot-dashboard/
---

# React Native IoT Dashboard

## 1. Project overview

This repository contains a React Native application designed as a dashboard for monitoring and managing IoT devices. It provides a mobile-oriented interface for viewing device telemetry, checking device status, and organizing information through reusable dashboard components.

Although it was listed in the portfolio as a clinical detection pipeline interface, the repository README describes an IoT monitoring dashboard. This documentation therefore reflects the implementation documented in the repository rather than attributing clinical functionality that is not evidenced there.

**Repository:** [React-Project](https://github.com/aditiprashant07/React-Project)

## 2. Objectives

- Build a cross-platform mobile interface for IoT device monitoring.
- Present incoming device data through a dashboard-oriented experience.
- Provide device status and management views.
- Organize the interface into reusable screens and components.
- Support a responsive, intuitive user experience.

## 3. Were the objectives achieved?

The repository README describes a React Native application with real-time IoT data visualization, device management and status monitoring, customizable widgets, and a reusable structure containing screens, components, and assets.

These features establish the intended scope and application structure. The README does not include screenshots, test results, a live telemetry integration description, or performance evidence. Accordingly, the repository supports describing the project as a React Native IoT dashboard implementation, but it does not independently verify the reliability of real-time device connectivity or the completion of every listed feature.

## 4. Concepts learned and applied

- React Native application development.
- Component-based UI architecture.
- Reusable screens and interface components.
- Cross-platform mobile application structure.
- State and data presentation for dashboard interfaces.
- Responsive layout and user experience design.
- Organizing assets, screens, and shared components.
- Node.js package management and development workflow.

## 5. Approach and what is different about it

### Approach

1. **Set up the mobile application.** Use React Native and the JavaScript ecosystem to create a cross-platform application.
2. **Organize the interface.** Separate screens, reusable components, and static assets into dedicated directories.
3. **Build the dashboard experience.** Present device telemetry and status information through a dashboard layout.
4. **Support device interaction.** Provide interface elements for monitoring and managing connected devices.
5. **Run the development workflow.** Install dependencies with npm or Yarn and launch the application using the configured project scripts (with Expo where applicable).

### What makes this approach distinctive?

The project focuses on presenting IoT monitoring in a mobile dashboard rather than treating device data as a command-line or backend-only output. Its component-and-screen organization provides a foundation for reusable widgets and a cross-platform interface.

The public README does not describe a custom data-ingestion backend or a clinical detection model, so neither is claimed here.

## 6. Technologies and tools used

| Technology | Role |
|---|---|
| React Native | Cross-platform mobile application framework |
| JavaScript | Application implementation |
| HTML | Web-related interface structure as listed in the repository |
| CSS | Styling and layout |
| Node.js | JavaScript runtime and development environment |
| npm / Yarn | Dependency management |
| Expo CLI | Optional development and run tooling, as described in the README |

## Limitations and next steps

- Add screenshots or a short demo showing the dashboard and device-monitoring flows.
- Document the telemetry source, device communication protocol, and update mechanism.
- Include setup instructions specific to the actual project configuration.
- Add tests for dashboard rendering, device states, and data updates.
- If this repository is intended to document a separate clinical detection interface, provide the correct repository so the portfolio page can be based on that implementation.
