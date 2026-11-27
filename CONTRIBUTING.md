# Contributing to Music Store

Thank you for your interest in contributing to the Music Store microservices demo!

## Code of Conduct

By participating in this project, you agree to abide by our [Code of Conduct](CODE_OF_CONDUCT.md).

## How to Contribute

### Reporting Bugs

- Search existing issues first
- Provide clear steps to reproduce
- Include environment details (Kubernetes version, Docker, etc.)

### Suggesting Enhancements

- Open an issue with the `enhancement` label
- Describe the proposed functionality and its value

### Pull Requests

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Make your changes
4. Ensure Docker builds succeed
5. Commit using Conventional Commits
6. Push and open a Pull Request

## Development Setup

```bash
git clone https://github.com/grlokesh96/music-store.git
cd music-store
docker compose up --build
```

## Commit Guidelines

Follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `chore`: Maintenance tasks

## Questions?

Open an issue with the `question` label.
