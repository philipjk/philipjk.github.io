---
layout: post
title: "Ski-touring Report Website"
date:   2025-03-04 10:18:14 +0100
categories: web development
---

## Project Overview
The Ski-touring Report is a website that provides real-time weather and 
avalanche reports for ski-touring enthusiasts. The project consists of a 
backend service that fetches data from various APIs and a frontend application 
that displays this data to users.

p.s. that is almost exclusively to play with modern API's and tools.

## Technologies and Languages Used
### Backend
- **FastAPI (Python):** A modern, fast (high-performance) web framework for building APIs with Python.
- **Uvicorn:** An ASGI server for serving FastAPI applications.
- **Requests:** A simple HTTP library for Python to make API requests.
- **OpenAI:** Used for analyzing weather data.
- **BeautifulSoup:** A library for web scraping to extract data from HTML and XML files.

### Frontend
- **React:** A JavaScript library for building user interfaces.
- **Vite:** A build tool that provides a faster and leaner development experience for modern web projects.
- **Axios:** A promise-based HTTP client for the browser and Node.js.
- **Tailwind CSS:** A utility-first CSS framework for rapidly building custom user interfaces.

## Tools and Platforms
- **Fly.io:** A platform for deploying and hosting the backend service.
- **Vercel:** A platform for deploying and hosting the frontend application.
- **Docker:** Used for containerizing the backend application for deployment.

## Deployment Details
### Backend
- The backend is built using FastAPI and deployed on Fly.io.
- It fetches weather and avalanche data from external APIs and serves it through a RESTful API.
- The backend is containerized using Docker and deployed using Fly.io's CLI.

### Frontend
- The frontend is built using React and Vite, providing a fast and responsive user interface.
- It fetches data from the backend and displays it to users in a user-friendly manner.
- The frontend is deployed on Vercel, ensuring fast and reliable delivery of the application.
