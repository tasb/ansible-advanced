# Handlers

## Run first time

```bash
ansible-playbook handlers.yml
```

Observe that nothing happens

## Make task to change host state

1. Uncomment `changed_when: true` in `handlers.yml`
2. Run playbook again

```bash
ansible-playbook handlers.yml
```
3. Check handlers are executed

## Force handlers execution

1. Uncomment `- meta: flush_handlers` in `handlers.yml`
2. Run playbook again

```bash
ansible-playbook handlers.yml
```
3. Check handlers executed right after task

## Force handlers on error

1. Uncomment apt task
2. Uncomment `force_handlers: True` in `handlers.yml`
3. Run playbook again

```bash
ansible-playbook handlers.yml
```
4. Check handlers executed even with error on a task