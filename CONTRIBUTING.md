# Contributing to k8s-stuck-pod-cleaner

Thank you for considering contributing to k8s-stuck-pod-cleaner! This document provides guidelines and instructions for contributing.

## Code of Conduct

By participating in this project, you agree to maintain respectful communication and inclusive behavior.

## How to Contribute

### Reporting Issues

If you find a bug or have a suggestion for improvement:

1. Check if the issue already exists in the GitHub Issues
2. If not, create a new issue with:
   - A clear title
   - A detailed description
   - Steps to reproduce (for bugs)
   - Expected behavior
   - Current behavior
   - Any relevant logs or screenshots

### Submitting Changes

1. Fork the repository
2. Create a new branch for your changes (`git checkout -b feature/your-feature-name`)
3. Make your changes
4. Test your changes:
   - Apply the YAML in a test Kubernetes cluster
   - Verify the CronJob runs correctly
   - Verify pods are properly cleaned up
5. Commit your changes with a clear commit message
6. Push to your branch
7. Submit a Pull Request

### Pull Request Process

1. Update the README.md with details of changes if applicable
2. Increase version numbers if applicable
3. The PR will be merged once it is approved by a maintainer

## Development Guidelines

### YAML Structure

- Keep the YAML file as a single file for easy deployment
- Use clear comments to document each section
- Follow Kubernetes best practices for resource naming and configuration

### Bash Script

- Add clear comments for each section of the script
- Include proper error handling
- Implement verbose logging for debugging
- Use consistent indentation and style

## Testing

Before submitting a PR, test your changes in a Kubernetes environment:

1. Apply the YAML to a test cluster
2. Create test pods that will enter a Terminating state
3. Verify the CronJob correctly identifies and cleans up stuck pods
4. Check the events to ensure proper recording

## Thank You

Your contributions help make Kubernetes administration easier for everyone!