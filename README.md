`ansible --version`

`ansible all -m ping`

`ansible all -m command -a "echo Hello World"`

`ansible-playbook my-environment.yaml`

`ansible-playbook -v my-environment.yaml`

# tag
`ansible-playbook my-environment.yaml --tags "onlyrun"`
```yaml
    - name: set oh-my-zsh
      tags:
        - onlyrun
      register: result
      ansible.builtin.shell: |
        wget --no-check-certificate https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
        chsh -s /bin/zsh vagrant
        zsh

    - name: Print return information from the previous task
      tags:
          - onlyrun
      ansible.builtin.debug:
        var: result
```

# lineinfile module
## 有regexp
`cat lineinfile.txt`
```
123
# port for http
www.*80/tcp
1234
1235
```
my-environment.yaml
```
- hosts: myhost
  become: yes #作為root執行 放這會作用每個task
  tasks:
    - name: set sudoers nopassword
      ansible.builtin.shell: |
        sed -i '/^%admin ALL=(ALL) ALL.*/a vagrant ALL=(ALL) NOPASSWD: ALL' /etc/sudoers
    - name: Ensure we have our own comment added to /etc/services
      become_user: vagrant
      ansible.builtin.lineinfile:
        path: ~/lineinfile.txt
        regexp: '^# port for http'
        insertbefore: '^www.*80/tcp'
        line: '# port for http by default'
      tags:
        - onlyrun1
```
`ansible-playbook my-environment.yaml --tags "onlyrun1"`
`cat lineinfile.txt`
```
123
# port for http by default
www.*80/tcp
1234
1235
```

## 沒regexp
```
    - name: Ensure we have our own comment added to /etc/services
      become_user: vagrant
      ansible.builtin.lineinfile:
        path: ~/lineinfile.txt
        insertafter: '^1235$'
        line: '8888'
      tags:
        - onlyrun1
```
`ansible-playbook my-environment.yaml --tags "onlyrun1"`
`cat lineinfile.txt`
```
123
# port for http by default
www.*80/tcp
1234
1235
8888
```
# async/poll
## poll 0
如果只是要開啟一個process放到背景執行，不需要檢查是否完成，poll設0即可
```yaml
    - name: install oh-my-zsh
      become_user: vagrant
      register: result
      async: 15
      poll: 0
      ansible.builtin.shell: |
        sh ~/install.sh
```
## 有設poll的檢查間隔秒數
```yaml
    - name: set oh-my-zsh
      async: 15
      poll: 5
      register: scrout
      ansible.builtin.shell: |
        chsh -s /bin/zsh vagrant
        zsh
```
# debug module
```yaml
    - name: set oh-my-zsh
      async: 15
      poll: 5
      register: scrout
      ansible.builtin.shell: |
        chsh -s /bin/zsh vagrant
        zsh
    - name: Checking the Job Status
      async_status:
        jid: "{{ scrout.ansible_job_id }}"
      register: job_result
      until: job_result.finished
      retries: 10
    - name: Print return information from the previous task
      ansible.builtin.debug:
        var: job_result
```
`Print return information from the previous task` Task Output:
```
TASK [Print return information from the previous task] *********************************************
ok: [10.1.7.111] => {
    "job_result": {
        "ansible_job_id": "179339402410.3598",
        "attempts": 1,
        "changed": true,
        "cmd": "chsh -s /bin/zsh vagrant\nzsh\n",
        "delta": "0:00:00.034260",
        "end": "2021-01-25 17:35:02.939990",
        "failed": false,
        "finished": 1,
        "rc": 0,
        "start": "2021-01-25 17:35:02.905730",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "",
        "stdout_lines": []
    }
}
```
# executable
```yaml
    - name: Run a command that uses non-posix shell-isms (in this example /bin/sh doesn't handle redirection and wildcards together but bash does)
      become_user: vagrant
      shell: 'echo ${{ env_variable }}'
      register: result
      args:
        executable: /bin/zsh
    - debug: msg="{{ result.stdout }}"
```