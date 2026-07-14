```makefile
.PHONY: setup dev test lint typecheck build check e2e clean

setup:
	<install command>

dev:
	<dev server command>

test:
	<test command>

lint:
	<lint command>

typecheck:
	<typecheck command or: @echo "no typecheck configured">

build:
	<build command>

# Full verification — must pass before any feature is marked passing
check: test lint typecheck build
	@echo "✓ All checks passed"

e2e:
	<e2e command or: @echo "no e2e configured yet">

clean:
	find . -name "*.log" -not -path "*/node_modules/*" -delete
	find . -name "debug-*" -not -path "*/node_modules/*" -delete
	@echo "✓ Cleaned"
```
