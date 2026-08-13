# Git Architecture & Internal Working 🏛️

To truly master Git, you must understand how it works under the hood. Git is not just a file-tracking system; it is essentially a **content-addressable filesystem** built on key object types.

---

## 1. The Three States & Four Areas

Git organizes files into **four distinct logical areas**:

```
+-----------------------------------------------------------------------------------+
|                                LOCAL MACHINE                                      |
|                                                                                   |
|  +-------------------+      +-------------------+      +----------------------+  |
|  | Working Directory | ---> |   Staging Area    | ---> |   Local Repository   |  |
|  |  (Untracked/Mod)  |      |   (Index/Cache)   |      |   (.git directory)   |  |
|  +-------------------+      +-------------------+      +----------------------+  |
|            |                          |                          |                |
+------------|--------------------------|--------------------------|----------------+
             |                          |                          |
             |  `git add`               |  `git commit`            |  `git push`
             +------------------------->+                          |
                                        +------------------------->+
                                                                   |
                                                                   v
                                                         +-------------------+
                                                         | Remote Repository |
                                                         |  (GitHub/GitLab)  |
                                                         +-------------------+
```

### Explanation of Areas:
1. **Working Directory**: Actual physical files on your disk. You edit, delete, or create files here.
2. **Staging Area (Index)**: A simple binary file (`.git/index`) containing a list of files that will go into the next commit.
3. **Local Repository (`.git` directory)**: Stores all object databases, refs, logs, and metadata for your project history.
4. **Remote Repository**: External server hosting the repository for team collaboration.

---

## 2. Git Object Architecture (The Core Data Model)

Git uses **4 main immutable object types** stored inside `.git/objects/`. Every object is compressed using `zlib` and indexed by a **40-character SHA-1 hash** (or SHA-256) calculated from its contents and header.

```
+------------------------------------------------------------------------+
|                              COMMIT OBJECT                             |
|  Hash: a1b2c3d...                                                      |
|  Tree: 8f9e10... (Points to Root Tree)                                 |
|  Parent: 4a5b6c... (Previous Commit)                                   |
|  Author: Jane Doe <jane@example.com>                                   |
|  Message: Initial Commit                                               |
+-----------------------------------+-+----------------------------------+
                                    |
                                    v
                       +-------------------------+
                       |       ROOT TREE         |
                       | Hash: 8f9e10...         |
                       +------------+------------+
                                    |
           +------------------------+------------------------+
           |                                                 |
           v                                                 v
+---------------------+                           +---------------------+
|     BLOB OBJECT     |                           |    SUB-TREE OBJECT  |
| Hash: e2f3g4...     |                           | Hash: 9d8c7b...     |
| Content: "hello"    |                           | (src/ folder)       |
+---------------------+                           +----------+----------+
                                                             |
                                                             v
                                                  +---------------------+
                                                  |     BLOB OBJECT     |
                                                  | Hash: 1a2b3c...     |
                                                  | Content: app code   |
                                                  +---------------------+
```

### The 4 Git Object Types:

#### 1. Blob (Binary Large Object)
- Stores **only the raw file contents**.
- Does **NOT** store file names, timestamps, or execution permissions.
- If two files in different directories have identical contents, Git stores only **one Blob**.

#### 2. Tree
- Represents a **directory structure**.
- Contains pointers to **Blobs** (files) and other **Trees** (subdirectories).
- Stores filename, file mode/permissions (e.g., `100644` for normal file, `100755` for executable), and the object's SHA-1 hash.

#### 3. Commit
- Represents a snapshot in time.
- Contains:
  - Pointer to the **Top-level Tree**.
  - Pointer(s) to **Parent Commit(s)** (0 for initial commit, 1 for normal commit, 2+ for merge commits).
  - Author and Committer metadata (Name, Email, Timestamp).
  - Commit message.

#### 4. Annotated Tag
- A permanent reference pointing to a specific commit.
- Contains tagger name, date, tag message, and GPG signature if signed.

---

## 3. Inside the `.git` Directory

When you run `git init`, Git creates a hidden `.git` folder containing:

```
.git/
├── HEAD            # Pointer to currently checked-out branch (e.g., ref: refs/heads/main)
├── config          # Project-specific configuration settings
├── description     # Used by GitWeb (rarely used today)
├── hooks/          # Client-side and server-side executable scripts (pre-commit, post-merge, etc.)
├── info/
│   └── exclude     # Local ignore patterns (not shared via remote)
├── objects/        # Database storing all Blobs, Trees, Commits, and Tags
│   ├── info/
│   └── pack/       # Compressed packfiles and index files
└── refs/           # Pointers to commits (branches, tags, remotes)
    ├── heads/      # Local branches (e.g., main, feature-1)
    ├── tags/       # Local tags (e.g., v1.0.0)
    └── remotes/    # Tracking branches for remote repositories
```

---

## 4. How Branches and HEAD Work

* **Branch**: A simple text file in `.git/refs/heads/<branch_name>` containing a single 40-character commit hash. Creating a branch takes **1 millisecond** and **zero extra storage** because it's just writing a 41-byte text file.
* **HEAD**: A simple text file (`.git/HEAD`) containing a reference to the active branch pointer (e.g., `ref: refs/heads/main`).
* **Detached HEAD State**: Occurs when `HEAD` points directly to a specific commit hash rather than a named branch pointer.

```
Normal HEAD state:
  HEAD ----> refs/heads/main ----> Commit [c3d4e5]

Detached HEAD state:
  HEAD --------------------------> Commit [c3d4e5]
```

---

## 5. Packfiles & Garbage Collection (`git gc`)

Initially, Git stores objects in **loose format** (one file per object). As the repository grows, Git performs **Garbage Collection**:
1. Compresses loose objects into a single binary **Packfile** (`.pack`).
2. Generates an Index file (`.idx`) for rapid object retrieval.
3. Uses **Delta Compression**: Stores base versions of files and subsequent incremental differences.