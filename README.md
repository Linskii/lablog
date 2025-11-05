# lablog

Research workflow memory system - never lose context when switching between projects.

## What is lablog?

lablog helps researchers track experiments, code changes, and todos across multiple projects. When you return to a project after weeks or months, `lablog recap` instantly reconstructs what you were working on and what's next.

**Powered by Claude Code**: lablog integrates with Claude Code to provide AI-powered script analysis, change summaries, and intelligent insights - all using your existing Claude Code subscription (no extra API costs!).

## Features

- 📝 **Experiment logging**: Track what you're running and why
- 🎯 **Smart todos**: Context-aware task tracking
- 🤖 **AI-powered analysis**: Use Claude to summarize scripts, analyze changes, and enhance recaps
- 🔄 **Auto-logging**: Shell wrappers for automatic logging of sbatch, git commits, etc.
- 📊 **Intelligent recaps**: Adaptive detail level based on task state
- 🌐 **Cross-project**: Works across multiple repos
- 💾 **Private storage**: Logs stored in `~/.config/lablog/`, not in git
- 🔍 **Job deduplication**: Prevents analyzing the same output file multiple times when jobs are re-run

## Installation

```bash
cd lablog
pip install -e .
```

## Quick Start

```bash
# Initialize in your project
lablog init

# Log an experiment (basic)
lablog log "Testing new architecture on 50k samples" sbatch jobs/train.sh

# Log with AI-powered script analysis (requires Claude Code)
lablog log "Running baseline model" sbatch jobs/train.sh --ai-summary

# Add todos
lablog todo "Check if preprocessing matches the paper"
lablog todo "Fix memory leak in data loader" --priority high

# Add notes
lablog note "Baseline achieved 0.85 AUROC"

# Get a recap of recent work
lablog recap

# AI-enhanced recap with insights (requires Claude Code)
lablog recap --enhanced
```

## Usage

### Basic Logging

```bash
# Log a note
lablog log "Discovered interesting pattern in embeddings"

# Log with command execution
lablog log "Training baseline" sbatch jobs/train.sh

# Log with AI summary of the script (uses Claude Code)
lablog log "Running ablation study" sbatch jobs/ablation.sh --ai-summary
```

### AI-Powered Features (Requires Claude Code)

```bash
# Summarize any script with Claude
lablog summarize jobs/train.sh
lablog summarize clique_prediction/models/main.py

# Analyze your uncommitted git changes
lablog analyze-diff

# Get AI-enhanced recap with insights
lablog recap --enhanced
lablog recap --enhanced --days 14
```

### Todos

```bash
lablog todo "Implement attention mechanism"
lablog todo "Fix memory leak in data loader" --priority high
```

### Recap

```bash
lablog recap              # Last 7 days
lablog recap --days 30    # Last 30 days
lablog recap --todos-only # Just show active todos
lablog recap --enhanced   # AI insights (requires Claude Code)
```

### Auto-Logging with Shell Wrappers

Generate shell functions that automatically log when you run commands:

```bash
# Generate wrappers
lablog generate-wrappers --shell bash --output ~/.lablog_wrappers.sh

# Add to your ~/.bashrc
echo "source ~/.lablog_wrappers.sh" >> ~/.bashrc
source ~/.bashrc

# Now sbatch and git commits are automatically logged!
sbatch jobs/train.sh  # Automatically logged with AI summary
git commit -m "Fix bug"  # Automatically logged
```

## Storage

Logs are stored in `~/.config/lablog/` by default, keeping them private and accessible across projects.

### Storage Structure

```
~/.config/lablog/
├── global.jsonl          # Cross-project entries
└── projects/
    └── <project-hash>/
        ├── metadata.json # Project info
        └── log.jsonl    # Project entries (JSONL format)
```

## Claude Code Integration

lablog integrates with [Claude Code](https://claude.com/claude-code) for AI-powered features. These features use Claude Code's headless mode, which means:

- ✅ Uses your existing Claude Code subscription
- ✅ No additional API costs
- ✅ Works from the command line
- ✅ Fully automated - no manual Claude interaction needed

### Requirements

To use AI-powered features:

1. Install [Claude Code](https://docs.claude.com/en/docs/claude-code/overview.md)
2. Make sure the `claude` command is in your PATH
3. Use flags like `--ai-summary` or `--enhanced` on lablog commands

If Claude Code is not installed, lablog still works perfectly - you just won't have AI analysis features.

### How It Works

When you run a command like `lablog summarize jobs/train.sh`, lablog:

1. Invokes Claude Code in headless mode: `claude -p "Your prompt"`
2. Gets the response automatically
3. Stores it in your log or displays it

No manual interaction required!

## Example Workflow

Here's a typical research workflow with lablog:

```bash
# Morning: Start working on a project
cd ~/my-research-project
lablog recap --enhanced  # Get AI summary of what you were doing

# Run an experiment with auto-logging
lablog log "Testing dropout=0.3" sbatch jobs/train.sh --ai-summary

# Made some code changes?
lablog analyze-diff  # Claude summarizes your changes

# End of day: Add a todo for tomorrow
lablog todo "Try learning rate warmup" --priority high

# Switch to another project
cd ~/protein-project
lablog recap  # Instantly see what you were doing here
```

## Tips for Researchers

1. **Use AI summaries sparingly**: The `--ai-summary` flag calls Claude, so use it for important experiments where you want detailed documentation

2. **Set up shell wrappers**: If you frequently use `sbatch`, auto-logging saves time:
   ```bash
   lablog generate-wrappers --shell bash --commands sbatch --output ~/.lablog_wrappers.sh
   ```

3. **Review regularly**: Run `lablog recap --enhanced` weekly to get AI insights on your progress

4. **Cross-project visibility**: Use `lablog recap --all-projects` to see all your work

5. **Export for papers**: The JSONL format is easy to parse for generating method sections

## License

MIT
