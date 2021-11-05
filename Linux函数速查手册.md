# Linux开发常用函数

## isspace

判断是否为空格

```c
#include <ctype.h>
int isspace ( int c );
```

> A value different from zero (i.e., `true`) if indeed *c* is a white-space character. Zero (i.e., `false`) otherwise.
>
> 是空格返回true非0值，不是空格返回false，零值。





# VFS文件系统

## struct dentry

```c
struct dentry {
	atomic_t d_count;
	unsigned int d_flags;		/* protected by d_lock */
	spinlock_t d_lock;		/* per dentry lock */
	int d_mounted;
	struct inode *d_inode;		/* Where the name belongs to - NULL is
					 * negative */
	/*
	 * The next three fields are touched by __d_lookup.  Place them here
	 * so they all fit in a cache line.
	 */
	struct hlist_node d_hash;	/* lookup hash list */
	struct dentry *d_parent;	/* parent directory */
	struct qstr d_name;

	struct list_head d_lru;		/* LRU list */
	/*
	 * d_child and d_rcu can share memory
	 */
	union {
		struct list_head d_child;	/* child of parent list */
	 	struct rcu_head d_rcu;
	} d_u;
	struct list_head d_subdirs;	/* our children */
	struct list_head d_alias;	/* inode alias list */
	unsigned long d_time;		/* used by d_revalidate */
	const struct dentry_operations *d_op;
	struct super_block *d_sb;	/* The root of the dentry tree */
	void *d_fsdata;			/* fs-specific data */

	unsigned char d_iname[DNAME_INLINE_LEN_MIN];	/* small names */
};
```



## struct inode





## struct address_space

```c
struct address_space {
	struct inode		*host;		/* owner: inode, block_device */
	struct radix_tree_root	page_tree;	/* radix tree of all pages */
	spinlock_t		tree_lock;	/* and lock protecting it */
	unsigned int		i_mmap_writable;/* count VM_SHARED mappings */
	struct prio_tree_root	i_mmap;		/* tree of private and shared mappings */
	struct list_head	i_mmap_nonlinear;/*list VM_NONLINEAR mappings */
	spinlock_t		i_mmap_lock;	/* protect tree, count, list */
	unsigned int		truncate_count;	/* Cover race condition with truncate */
	unsigned long		nrpages;	/* number of total pages */
	pgoff_t			writeback_index;/* writeback starts here */
	const struct address_space_operations *a_ops;	/* methods */
	unsigned long		flags;		/* error bits/gfp mask */
	struct backing_dev_info *backing_dev_info; /* device readahead, etc */
	spinlock_t		private_lock;	/* for use by the address_space */
	struct list_head	private_list;	/* ditto */
	struct address_space	*assoc_mapping;	/* ditto */
	struct mutex		unmap_mutex;    /* to protect unmapping */
} __attribute__((aligned(sizeof(long))));
```





## struct page

```c
/*
 * Each physical page in the system has a struct page associated with
 * it to keep track of whatever it is we are using the page for at the
 * moment. Note that we have no way to track which tasks are using
 * a page, though if it is a pagecache page, rmap structures can tell us
 * who is mapping it.
 */
struct page {
	unsigned long flags;		/* Atomic flags, some possibly
					 * updated asynchronously */
	atomic_t _count;		/* Usage count, see below. */
	union {
		atomic_t _mapcount;	/* Count of ptes mapped in mms,
					 * to show when page is mapped
					 * & limit reverse map searches.
					 */
		struct {		/* SLUB */
			u16 inuse;
			u16 objects;
		};
	};
	union {
	    struct {
		unsigned long private;		/* Mapping-private opaque data:
					 	 * usually used for buffer_heads
						 * if PagePrivate set; used for
						 * swp_entry_t if PageSwapCache;
						 * indicates order in the buddy
						 * system if PG_buddy is set.
						 */
		struct address_space *mapping;	/* If low bit clear, points to
						 * inode address_space, or NULL.
						 * If page mapped as anonymous
						 * memory, low bit is set, and
						 * it points to anon_vma object:
						 * see PAGE_MAPPING_ANON below.
						 */
	    };
#if USE_SPLIT_PTLOCKS
	    spinlock_t ptl;
#endif
	    struct kmem_cache *slab;	/* SLUB: Pointer to slab */
	    struct page *first_page;	/* Compound tail pages */
	};
	union {
		pgoff_t index;		/* Our offset within mapping. */
		void *freelist;		/* SLUB: freelist req. slab lock */
	};
	struct list_head lru;		/* Pageout list, eg. active_list
					 * protected by zone->lru_lock !
					 */
	/*
	 * On machines where all RAM is mapped into kernel address space,
	 * we can simply calculate the virtual address. On machines with
	 * highmem some memory is mapped into kernel virtual memory
	 * dynamically, so we need a place to store that address.
	 * Note that this field could be 16 bits on x86 ... ;)
	 *
	 * Architectures with slow multiplication can define
	 * WANT_PAGE_VIRTUAL in asm/page.h
	 */
#if defined(WANT_PAGE_VIRTUAL)
	void *virtual;			/* Kernel virtual address (NULL if
					   not kmapped, ie. highmem) */
#endif /* WANT_PAGE_VIRTUAL */
#ifdef CONFIG_WANT_PAGE_DEBUG_FLAGS
	unsigned long debug_flags;	/* Use atomic bitops on this */
#endif

#ifdef CONFIG_KMEMCHECK
	/*
	 * kmemcheck wants to track the status of each byte in a page; this
	 * is a pointer to such a status block. NULL if not tracked.
	 */
	void *shadow;
#endif
};
```







## super_operations  

```c
struct super_operations { 
    	struct inode *(*alloc_inode)(struct super_block *sb);
        void (*destroy_inode)(struct inode *);
        void (*dirty_inode) (struct inode *);
        int (*write_inode) (struct inode *, int);
        void (*drop_inode) (struct inode *);
        void (*delete_inode) (struct inode *);
        void (*put_super) (struct super_block *);
        void (*write_super) (struct super_block *);
        int (*sync_fs)(struct super_block *sb, int wait);
        int (*freeze_fs) (struct super_block *);
        int (*unfreeze_fs) (struct super_block *);
        int (*statfs) (struct dentry *, struct kstatfs *);
        int (*remount_fs) (struct super_block *, int *, char *);
        void (*clear_inode) (struct inode *);
        void (*umount_begin) (struct super_block *);
        int (*show_options)(struct seq_file *, struct vfsmount *);
        ssize_t (*quota_read)(struct super_block *, int, char *, size_t, loff_t); ssize_t (*quota_write)(struct super_block *, int, const char *, size_t, loff_t); };
```



### alloc_inode

**定义**

```c
struct inode *(*alloc_inode)(struct super_block *sb);
```



**功能**





**参数**

- `dir_inode` 为父文件夹节点



**返回值**





### destroy_inode

**定义**

```c
void (*destroy_inode)(struct inode *);
```



**功能**





**参数**

- `dir_inode` 为父文件夹节点



**返回值**







### drop_inode

**定义**

```c
void (*drop_inode) (struct inode *);
```



**功能**





**参数**

- `dir_inode` 为父文件夹节点



**返回值**





### clear_inode

**定义**

```c
void (*clear_inode) (struct inode *);
```



**功能**





**参数**

- `dir_inode` 为父文件夹节点



**返回值**







## inode_operations

```c
struct inode_operations {
	int (*create) (struct inode *,struct dentry *,int, struct nameidata *);
	struct dentry * (*lookup) (struct inode *,struct dentry *, struct nameidata *);
	int (*link) (struct dentry *,struct inode *,struct dentry *);
	int (*unlink) (struct inode *,struct dentry *);
	int (*symlink) (struct inode *,struct dentry *,const char *);
	int (*mkdir) (struct inode *,struct dentry *,int);
	int (*rmdir) (struct inode *,struct dentry *);
	int (*mknod) (struct inode *,struct dentry *,int,dev_t);
	int (*rename) (struct inode *, struct dentry *,
			struct inode *, struct dentry *);
	int (*readlink) (struct dentry *, char __user *,int);
	void * (*follow_link) (struct dentry *, struct nameidata *);
	void (*put_link) (struct dentry *, struct nameidata *, void *);
	void (*truncate) (struct inode *);
	int (*permission) (struct inode *, int);
	int (*check_acl)(struct inode *, int);
	int (*setattr) (struct dentry *, struct iattr *);
	int (*getattr) (struct vfsmount *mnt, struct dentry *, struct kstat *);
	int (*setxattr) (struct dentry *, const char *,const void *,size_t,int);
	ssize_t (*getxattr) (struct dentry *, const char *, void *, size_t);
	ssize_t (*listxattr) (struct dentry *, char *, size_t);
	int (*removexattr) (struct dentry *, const char *);
	void (*truncate_range)(struct inode *, loff_t, loff_t);
	long (*fallocate)(struct inode *inode, int mode, loff_t offset,
			  loff_t len);
	int (*fiemap)(struct inode *, struct fiemap_extent_info *, u64 start,
		      u64 len);
};
```

- create: 由 open(2) 和 creat(2) 系统调用调用。 仅当您想支持常规文件时才需要。 你得到的 dentry 不应该有一个 inode（即它应该是一个负的 dentry）。 在这里，您可能会使用 dentry 和新创建的 inode 调用 d_instantiate()

- lookup:当 VFS 需要在父目录中查找 inode 时调用。 要查找的名称可在 dentry 中找到。 此方法必须调用 d_add() 将找到的 inode 插入到 dentry 中。  inode 结构中的“i_count”字段应该递增。 如果指定的 inode 不存在，则应将 NULL inode 插入到 dentry 中（这称为否定 dentry）。 从这个例程返回错误代码必须只在真正的错误时完成，否则创建具有系统调用的 inode 将失败，如 create(2)、mknod(2)、mkdir(2) 等。

  如果您希望重载 dentry 方法，那么您应该初始化 dentry 中的“d_dop”字段； 这是一个指向结构“dentry_operations”的指针。

  这个方法是用持有的目录 inode 信号量调用的

- link:由 link(2) 系统调用调用。 仅当您想支持硬链接时才需要。 您可能需要像在 create() 方法中一样调用 d_instantiate()

- unlink:由 unlink(2) 系统调用调用。 仅当您想支持删除 inode 时才需要

- symlink:由 symlink(2) 系统调用调用。 仅当您想支持符号链接时才需要。 您可能需要像在 create() 方法中一样调用 d_instantiate()

- mkdir:由 mkdir(2) 系统调用调用。 仅当您想支持创建子目录时才需要。 您可能需要像在 create() 方法中一样调用 d_instantiate()

- rmdir:由rmdir(2)系统调用。 仅当您希望支持删除子目录时才需要

- mknod:由Mknod（2）系统调用调用以创建设备（Char，块）inode或命名管道（FIFO）或套接字。 只有需要如果要支持创建这些类型的inodes，则才需要。 您可能需要在Create（）方法中时调用d_instantiatiate（）。

- rename:由rename（2）系统调用重命名对象以具有由第二个inode和endry给出的父级和名称。

- readlink:由ReadLink（2）系统调用调用。 只有需要支持阅读符号链接时才需要

- follow_link: called by the VFS to follow a symbolic link to the inode it points to.  Only required if you want to support symbolic links.  This method returns a void pointer cookie that is passed to put_link().

- put_link: called by the VFS to release resources allocated by follow_link().  The cookie returned by follow_link() is passed to this method as the last parameter.  It is used by filesystems such as NFS where page cache is not stable (i.e. page that was installed when the symbolic link walk started might not be in the page cache at the end of the walk). 

- truncate: Deprecated. This will not be called if ->setsize is defined. Called by the VFS to change the size of a file.  The i_size field of the inode is set to the desired size by the VFS before this method is called.  This method is called by the truncate(2) system call and related functionality.
  Note: ->truncate and vmtruncate are deprecated. Do not add new instances/calls of these. Filesystems should be converted to do their truncate sequence via ->setattr().

- permission:由VFS调用以检查POSIX的文件系统上的访问权限

- setattr:由VFS调用以设置文件的属性。 CHMOD（2）和相关系统调用调用此方法

- getattr:由VFS调用以获取文件的属性。该方法由Stat（2）和相关系统调用调用

- setxattr:由VFS调用以设置文件的扩展属性。SetXATTR（2）系统调用调用此方法

- getxattr:由VFS调用以检索扩展属性名称的值。此方法由GetXATTR（2）函数调用调用

- listxattr：由VFS调用以列出给定文件的所有扩展属性。该方法由ListXATTR（2）系统调用调用

- RemoveXATTR：由VFS调用以从文件中删除扩展属性。该方法由RemoveXATTR（2）系统调用调用

- truncate_range: a method provided by the underlying filesystem to truncate a range of blocks , i.e. punch a hole somewhere in a file



### create

**定义**

```c
int (*create) (struct inode *dir_inode,struct dentry *dentry,int mode, struct nameidata *nd);
```



**功能**

由 `open(2)` 和 `creat(2)` 系统调用调用。 仅当您想支持常规文件时才需要。

由open和create系统调用使用。入参inode为父目录的inode，入参dentry为新创建的，没有对应的inode（negative dentry）。底层文件系统需要调用d_instantiate()将dentry和新创建的inode进行关联。只有目录类型的inode才会调用该函数指针。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### lookup

**定义**

```c
struct dentry * (*lookup) (struct inode *dir_inode,struct dentry *dentry, struct nameidata *);
```



**功能**

当 VFS 需要在父目录中查找 inode 时调用。 此方法必须调用 `d_add()` 将找到的 inode 插入到 dentry 中。  inode 结构中的“`i_count`”字段应该递增。 如果指定的 inode 不存在，则应将 NULL inode 插入到 dentry 中（这称为负 dentry）。

VFS需要查找目录下面某个inode信息是调用该函数。入参dentry里携带了要查找的文件name。该函数里需要调用d_add()将找到的inode插入到dentry。并且inode的i_count字段需要递增。如果inode没有找到，则dentry插入一个NULL inode（这种dentry称为一个negative dentry）。只有在底层真实错误时才能返回error，此时open、create、mknode等涉及创建inode的操作都会失败。同样也只有目录类型的inode才会调用该函数指针。

在*lookup*函数里，可以将dentry的d_op字段初始化为自己的dentry_operations，来定制对dentry和dcache的一些管理函数操作合集。



**参数**

- `dir_inode` 为父文件夹节点。
- `dentry` 要查找的名称可在 dentry 中找到。



**返回值**

 从这个例程返回错误代码必须只在真正的错误时完成，否则创建具有系统调用的 inode 将失败，如 create(2)、mknod(2)、mkdir(2) 等。



### link

**定义**

```c
int (*link) (struct dentry *,struct inode *,struct dentry *);
```



**功能**

link系统调用使用，用于创建硬链接。同样需要调用d_instantiate()来关联dentry和inode。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### unlink

**定义**

```c
int (*unlink) (struct inode *,struct dentry *);
```



**功能**

unlink系统调用使用，用于删除一个inode关联的文件或目录。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### symlink

**定义**

```c
int (*symlink) (struct inode *,struct dentry *,const char *);
```



**功能**

symlink系统调用使用，用于创建一个软链接。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### mkdir

**定义**

```c
int (*mkdir) (struct inode *,struct dentry *,int);
```



**功能**

mkdir系统调用使用，用于创建一个子目录。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### rmdir

**定义**

```c
int (*rmdir) (struct inode *,struct dentry *);
```



**功能**

rmdir系统调用使用，用于删除一个子目录。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### mknod

**定义**

```c
int (*mknod) (struct inode *,struct dentry *,int,dev_t);
```



**功能**

mknod系统调用使用，用于创建一个设备inode(char,block)或者一个named pipe (FIFO)或者一个socket。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### rename

**定义**

```c
int (*rename) (struct inode *, struct dentry *,
		struct inode *, struct dentry *);
```



**功能**

rename系统调用使用，用于改名。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**





### readlink

**定义**

```c
int (*readlink) (struct dentry *, char __user *,int);
```



**功能**

readlink系统调用使用，用于读取软链接文件指向的实际路径。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### follow_link

**定义**

```c
void * (*follow_link) (struct dentry *, struct nameidata *);
```



**功能**

VFS调用，用于跟踪获取一个软链接指向的inode。该函数返回一个指针cookie，该cookie会传递给*put_link*。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### put_link

**定义**

```c
void (*put_link) (struct dentry *, struct nameidata *, void *);
```



**功能**

用于释放*follow_link*里申请的资源，cookie作为最后一个参数传入。它在NFS等文件系统上，page cache不是很稳定的情况下使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### permission

**定义**

```c
int (*permission) (struct inode *inode, int mask);
```



**功能**

VFS调用，用于检测访问权限。有可能在rcu-walk mode下被调用，那么该函数必须不能阻塞或者存储数据到inode。如果在rcu-walk mode下遇到问题，则返回-ECHILD，它将在ref-walk mode重新被调用。



**参数**

- `inode` 为要检查文件的节点

- `mask`  为要验证的权限，如下，通常是组合，当打开一个文件的时候，会是32+4，写入的时候会16+2

  ```c
  #define MAY_EXEC 1
  #define MAY_WRITE 2
  #define MAY_READ 4
  #define MAY_APPEND 8
  #define MAY_ACCESS 16
  #define MAY_OPEN 32
  ```



**返回值**

权限验证通过后返回0，无权限的返回`-EACCES`。



#### inode_permission 源码分析

```c
/**
 * inode_permission  -  check for access rights to a given inode
 * @inode:	inode to check permission on
 * @mask:	right to check for (%MAY_READ, %MAY_WRITE, %MAY_EXEC)
 *
 * Used to check for read/write/execute permissions on an inode.
 * We use "fsuid" for this, letting us set arbitrary permissions
 * for filesystem access without changing the "normal" uids which
 * are used for other things.
 */
int inode_permission(struct inode *inode, int mask)
{
	int retval;

	if (mask & MAY_WRITE) {
		umode_t mode = inode->i_mode;

		/*
		 * Nobody gets write access to a read-only fs.
		 */
		if (IS_RDONLY(inode) &&
		    (S_ISREG(mode) || S_ISDIR(mode) || S_ISLNK(mode)))
			return -EROFS;

		/*
		 * Nobody gets write access to an immutable file.
		 */
		if (IS_IMMUTABLE(inode))
			return -EACCES;
	}

	if (inode->i_op->permission)
		retval = inode->i_op->permission(inode, mask);
	else
		retval = generic_permission(inode, mask, inode->i_op->check_acl);

	if (retval)
		return retval;

	retval = devcgroup_inode_permission(inode, mask);
	if (retval)
		return retval;

	return security_inode_permission(inode, mask);
}
```







### setattr

**定义**

```c
int (*setattr) (struct dentry *, struct iattr *);
```



**功能**

VFS调用，用于设置文件的attr属性。它将被chmod等相关系统调用使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### getattr

**定义**

```c
int (*getattr) (struct vfsmount *mnt, struct dentry *, struct kstat *);
```



**功能**

VFS调用，用于获取文件的attr属性。它将被stat等相关系统调用使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### setxattr

**定义**

```c
int (*setxattr) (struct dentry *, const char *,const void *,size_t,int);
```



**功能**

VFS调用，用于设置文件的一个扩展attr属性。它将被setxattr系统调用使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**





### getxattr

**定义**

```c
ssize_t (*getxattr) (struct dentry *, const char *, void *, size_t);
```



**功能**

VFS调用，用于根据属性名称获取文件的一个扩展attr属性。它将被getxattr系统调用使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### listxattr

**定义**

```c
ssize_t (*listxattr) (struct dentry *, char *, size_t);
```



**功能**

VFS调用，用于列出给定文件的所有扩展属性。它将被listxattr系统调用使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**



### removexattr

**定义**

```c
int (*removexattr) (struct dentry *, const char *);
```



**功能**

VFS调用，用于删除一个扩展attr属性。它将被removexattr系统调用使用。



**参数**

- `dir_inode` 为父文件夹节点
- `dentry` 为要创建的文件目录项，此时dentry中的inode成员未指定（即它应该是一个负的 dentry， 在这里，您可能会使用 dentry 和新创建的 inode 调用 `d_instantiate()`。
- `mode` 
- `nd` 



**返回值**





## file_operations

```c
/*
 * NOTE:
 * read, write, poll, fsync, readv, writev, unlocked_ioctl and compat_ioctl
 * can be called without the big kernel lock held in all filesystems.
 */
struct file_operations {
	struct module *owner;
	loff_t (*llseek) (struct file *, loff_t, int);
	ssize_t (*read) (struct file *, char __user *, size_t, loff_t *);
	ssize_t (*write) (struct file *, const char __user *, size_t, loff_t *);
	ssize_t (*aio_read) (struct kiocb *, const struct iovec *, unsigned long, loff_t);
	ssize_t (*aio_write) (struct kiocb *, const struct iovec *, unsigned long, loff_t);
	int (*readdir) (struct file *, void *, filldir_t);
	unsigned int (*poll) (struct file *, struct poll_table_struct *);
	int (*ioctl) (struct inode *, struct file *, unsigned int, unsigned long);
	long (*unlocked_ioctl) (struct file *, unsigned int, unsigned long);
	long (*compat_ioctl) (struct file *, unsigned int, unsigned long);
	int (*mmap) (struct file *, struct vm_area_struct *);
	int (*open) (struct inode *, struct file *);
	int (*flush) (struct file *, fl_owner_t id);
	int (*release) (struct inode *, struct file *);
	int (*fsync) (struct file *, struct dentry *, int datasync);
	int (*aio_fsync) (struct kiocb *, int datasync);
	int (*fasync) (int, struct file *, int);
	int (*lock) (struct file *, int, struct file_lock *);
	ssize_t (*sendpage) (struct file *, struct page *, int, size_t, loff_t *, int);
	unsigned long (*get_unmapped_area)(struct file *, unsigned long, unsigned long, unsigned long, unsigned long);
	int (*check_flags)(int);
	int (*flock) (struct file *, int, struct file_lock *);
	ssize_t (*splice_write)(struct pipe_inode_info *, struct file *, loff_t *, size_t, unsigned int);
	ssize_t (*splice_read)(struct file *, loff_t *, struct pipe_inode_info *, size_t, unsigned int);
	int (*setlease)(struct file *, long, struct file_lock **);
};
```





## dentry_operations

```c
struct dentry_operations {
	int (*d_revalidate)(struct dentry *, struct nameidata *);
	int (*d_hash) (struct dentry *, struct qstr *);
	int (*d_compare) (struct dentry *, struct qstr *, struct qstr *);
	int (*d_delete)(struct dentry *);
	void (*d_release)(struct dentry *);
	void (*d_iput)(struct dentry *, struct inode *);
	char *(*d_dname)(struct dentry *, char *, int);
};
```



### d_revalidate

**定义**

```c
int (*d_revalidate)(struct dentry *, struct nameidata *);
```



**功能**

VFS用于检查在dcache里找到的dentry是否有效。通常设置为NULL，则只要在dcache找到即认为是有效的。但对网络文件系统如NFS来说，dentry可能在一段时间之后就会失效，因此需要实现该函数用于检查是否有效。如果有效，函数需要返回一个正数。

*d_revalidate*可能在rcu-walk模式（flags & LOOKUP_RCU）下被调用。此时该函数里不能阻塞也不能写入数据到dentry，并且d_parent和d_inode不能使用，因为他们可能瞬间就可能被修改。如果在rcu-walk模式遇到困难，则返回-ECHILD，将在ref-walk模式下重新调用。



**参数**

- `page` 



**返回值**



### d_hash

**定义**

```c
int (*d_hash) (struct dentry *, struct qstr *);
```



**功能**

用于VFS将dentry放入HASH列表。并不清楚HASH表用来做啥，通常不需要设置它，使用VFS默认的即可。



**参数**

- `page` 



**返回值**





### d_compare

**定义**

```c
int (*d_compare) (struct dentry *, struct qstr *, struct qstr *);
```



**功能**

用于比较dentry name和指定的name。该函数必须是可重入的，即每次的返回结果一样。



**参数**

- `page` 



**返回值**





### d_delete

**定义**

```c
int (*d_delete)(struct dentry *);
```



**功能**

用于引用计数递减为0时调用，返回1则dcache立即删除dentry，返回0则继续缓存该dentry。默认为NULL，则总是将dentry进行缓存。该函数必须是可重入的，即每次的返回结果一样。



**参数**

- `page` 



**返回值**





### d_release

**定义**

```c
void (*d_release)(struct dentry *);
```



**功能**

用于释放dentry资源。



**参数**

- `page` 



**返回值**





### d_iput

**定义**

```c
void (*d_iput)(struct dentry *, struct inode *);
```



**功能**

用于释放dentry对应inode引用计数。该函数在释放dentry之前调用。如果为NULL，则VFS默认调用iput()。



**参数**

- `page` 



**返回值**





### d_dname

**定义**

```c
char *(*d_dname)(struct dentry *, char *, int);
```



**功能**

用于生成dentry的pathname，主要是一些伪文件系统（sockfs, pipefs等）用于延迟生成pathname。一般文件系统不实现该函数，因为其dentry存在于dcache的hash表里（通过pathname做hash），所以并不希望pathname变化。



**参数**

- `page` 



**返回值**













## address_space_operations

[Linux VFS机制简析（二） - 舰队 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jimbo17/p/10119567.html)



```c
struct address_space_operations {
	int (*writepage)(struct page *page, struct writeback_control *wbc);
	int (*readpage)(struct file *, struct page *);
	int (*sync_page)(struct page *);
	int (*writepages)(struct address_space *, struct writeback_control *);
	int (*set_page_dirty)(struct page *page);
	int (*readpages)(struct file *filp, struct address_space *mapping,
			struct list_head *pages, unsigned nr_pages);
	int (*write_begin)(struct file *, struct address_space *mapping,
				loff_t pos, unsigned len, unsigned flags,
				struct page **pagep, void **fsdata);
	int (*write_end)(struct file *, struct address_space *mapping,
				loff_t pos, unsigned len, unsigned copied,
				struct page *page, void *fsdata);
	sector_t (*bmap)(struct address_space *, sector_t);
	int (*invalidatepage) (struct page *, unsigned long);
	int (*releasepage) (struct page *, int);
	ssize_t (*direct_IO)(int, struct kiocb *, const struct iovec *iov,
			loff_t offset, unsigned long nr_segs);
	struct page* (*get_xip_page)(struct address_space *, sector_t,
			int);
	/* migrate the contents of a page to the specified target */
	int (*migratepage) (struct page *, struct page *);
	int (*launder_page) (struct page *);
	int (*error_remove_page) (struct mapping *mapping, struct page *page);
};
```

Address Space用于管理page caches里的page页，它关联某个文件的所有pages，并管理文件的内容到进程空间的映射。它还提供了内存管理接口：page回收、根据地址查找page、跟踪地址查找page、跟踪page的tags(如dirty和writeback)等等功能。

VM模块会调用`write_page`方法去尝试将脏页刷盘，以及调用`releasepage`将clean page释放。带有PagePrivate标记的clean page（引用为0）会被VM直接释放而不通知Address Space。

为了实现这个功能，Address Space通过lru_cache_add()将page放入LRU，并通过mark_page_active()标记page正在使用。

Pages通过->index保存在一个radix树里，该radix树维护page的PG_Dirty和PG_Writeback信息，因此查找这两个标识的pages变得非常快。

Dirty标记（*PAGECACHE_TAG_DIRTY*）主要由->writepages（默认方法mpage_writepages()）方法使用。它使用该标记查找脏页并调用->writepage方法。如果Address operations实现了自己的->writepages（不使用mpage_writepages），则Dirty标记将几乎没有作用。write_inode_now()和sync_inode()通过Dirty标记来检查->writepages是否成功完成。

Writeback标记主要是由filemap_*wait* 方法和sync_page* 方法使用，通过调用filemap_fdatawait_range()等待所有的writeback完成。如果定义了->sync_page，则会调用它来等待所有需要writeback的page结束。

Address Space Handler可以通过page的*private*字段保存额外的数据，此时需要设置PG_Private标识。这样VM的相关操作会调用address的handler处理这些数据。

上面说的这么多Page相关的管理，**其实Address Space最核心的作用是充当存储和应用程序的中间缓存。**数据从存储侧以page为单位读入address space，通过拷贝或者mapping的方式提供给应用层。应用写入数据到address space，然后通过writeback机制写入到存储。

读操作的核心是*readpage()*。写操作稍微复杂些，可以通过*write_begin*/*write_end*或者*set_page_dirty*写入数据到address space，再通过*writepage*、*sync_page*和*writepages*写入数据到存储。从address space里增加删除page由inode的*i_mutex*锁保护。

当数据写入到page，需要设置PG_Dirty标识，当writepage准备写入存储时清除PG_Dirty，并设置PG_Writeback标识，知道数据完全写入存储后清除PG_Writeback。



### writepage

**定义**

```c
int (*writepage)(struct page *page, struct writeback_control *wbc);
```



**功能**

VM调用，用于将脏页写入后端存储。参数wbc->sync_mode显示是什么原因触发，'sync'或者'flush'（释放内存）。调用时PG_Dirty已经被清除，并且PageLocked已经设置。*writepage*开始写入数据时需要设置PG_Writeback，并且写入结束时清除该标记。**无论是同步还是异步写入，都要保证函数返回时page处于unlocked状态。**
如果wbc->sync_mode是WB_SYNC_NONE（不等待），则*writepage*遇到困难时可以不那么努力的写入，而是返回AOP_WRITEPAGE_ACTIVATE，这样VM不会老是来写该page。



**参数**

- `page` 



**返回值**



### readpage

**定义**

```c
int (*readpage)(struct file *, struct page *);
```



**功能**

VM调用，用于从后端存储读取数据。调用时，page处于lock状态，并且在读取结束时需要设置为unlock状态，并设置uptodate。如果*readpage*处理过程中需要unlock page，则unlcok之后需要返回AOP_TRUNCATED_PAGE，调用者将重新定位page并重新lock，成功之后会再次调用*readpage*。



**参数**

- `page` 



**返回值**



#### 读文件函数栈流程

以内核`2.6.32-754`为例：

```c
ecryptfs_readpage(struct file * file, struct page * page) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\fs\ecryptfs\mmap.c:199)
read_pages(unsigned int nr_pages, struct list_head * pages, struct file * filp, struct address_space * mapping) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\readahead.c:126)
__do_page_cache_readahead(struct address_space * mapping, struct file * filp, unsigned long offset, unsigned long nr_to_read, unsigned long lookahead_size) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\readahead.c:192)
ra_submit(struct file_ra_state * ra, struct address_space * mapping, struct file * filp) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\readahead.c:251)
ondemand_readahead(struct address_space * mapping, struct file_ra_state * ra, struct file * filp, bool hit_readahead_marker, unsigned long offset, unsigned long req_size) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\readahead.c:481)
page_cache_sync_readahead(struct address_space * mapping, struct file_ra_state * ra, struct file * filp, unsigned long offset, unsigned long req_size) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\readahead.c:513)
do_generic_file_read(read_descriptor_t * desc, loff_t * ppos, struct file * filp) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\filemap.c:1134)
generic_file_aio_read(struct kiocb * iocb, const struct iovec * iov, unsigned long nr_segs, loff_t pos) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\mm\filemap.c:1486)
ecryptfs_read_update_atime(struct kiocb * iocb, const struct iovec * iov, unsigned long nr_segs, loff_t pos) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\fs\ecryptfs\file.c:55)
do_sync_read(struct file * filp, char * buf, size_t len, loff_t * ppos) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\fs\read_write.c:325)
vfs_read(struct file * file, char * buf, size_t count, loff_t * pos) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\fs\read_write.c:354)
sys_read(unsigned int fd, char * buf, size_t count) (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\fs\read_write.c:443)
system_call() (\root\rpmbuild\SOURCES\linux-2.6.32-754.el6\arch\x86\kernel\entry_64.S:521)
[Unknown/Just-In-Time compiled code] (未知源:0)
```







### write_begin

**定义**

```c
int (*write_begin)(struct file *pf, struct address_space *mapping,
			loff_t pos, unsigned len, unsigned flags,
			struct page **pagep, void **fsdata);
```



**功能**

由写入缓存代码调用。让文件系统准备在给定的文件`pf`、偏移位置`pos`及写入长度`len`个字节。这个函数需要检查写入是否能够完成，以及在需要的时候分配空间(创建新页)，并更新配置页。

函数为指定的页面返回锁定的页面缓存`*pagep`，供调用者写入。

由通用的buffered写流程调用，写入len长度数据到文件的offset处。address space可能需要申请额外的存储空间来保证写操作可以完成，或者需要从后端存储读取不在缓存里的pages。该函数返回的pagep要处于locked状态，调用者将直接写入数据。返回参数fsdata用于私有数据指针，它将传递给*write_end*函数。如果函数返回<0，则*write_end*将不会调用。



**参数**

- `pf` 



**返回值**







### write_end

**定义**

```c
int (*write_end)(struct file *pf, struct address_space *mapping,
			loff_t pos, unsigned len, unsigned copied,
			struct page *page, void *fsdata);
```



**功能**

当`write_begin`成功后，数据进行拷贝，此时必须调用`write_end`。`len`是传递给`write_begin`的原始长度，`copied`是能够复制的数量。

此函数必须负责解锁页面并释放它的引用计数，以及更新`i_size`。

**真正写入到文件缓存是在这里。**



数据拷贝到*write_begin*返回的page后，**调用*write_end*将page unlock，递减引用计数并更新i_size字段**。



**参数**

- `pf` 



**返回值**

失败时返回<0，否则返回能够复制到页面缓存中的字节数(<= copied)。



### bmap

**定义**

```c
sector_t (*bmap)(struct address_space *, sector_t);
```



**功能**

*暂不理解此功能*

由VFS调用以将对象内的逻辑块偏移映射到物理块号。为了能够交换到文件，该文件必须具有到块设备的稳定映射。 交换系统不通过文件系统，而是使用 bmap 找出文件中的块所在的位置并直接使用这些地址。

VFS调用用于映射逻辑块的偏移和物理块编号。该方法由FIBMAP ioctl使用，并且是swap文件。swap系统不直接进入文件系统，而是通过BMAP方式建立内存地址和文件的块映射，然后直接使用内存地址。



**参数**

- `pf` 



**返回值**

失败时返回<0，否则返回能够复制到页面缓存中的字节数(<= copied)。






## path_lookup/kern_path

> name:路径名
>
> flags:查找操作的标识
>
> struct nameidata:用于保存当前的相关查找结果

```c
int path_lookup(const char *name, unsigned int flags, 
      struct nameidata *nd) 
{ 
  return do_path_lookup(AT_FDCWD, name, flags, nd); 
} 
```

这里可以看到path_lookup()只是对do_path_lookup()的一层封装

```c
static int do_path_lookup(int dfd, const char *name,
				unsigned int flags, struct nameidata *nd)
{
	struct filename filename = { .name = name };

	return filename_lookup(dfd, &filename, flags, nd);
}

/* Returns 0 and nd will be valid on success; Retuns error, otherwise. */
static int filename_lookup(int dfd, struct filename *filename,
				unsigned int flags, struct nameidata *nd)
{
	int retval = path_init(dfd, filename->name, flags, nd);
	if (!retval)
		retval = path_walk(filename, nd);
	if (unlikely(!retval && !audit_dummy_context() && nd->path.dentry &&
				nd->path.dentry->d_inode))
		audit_inode(filename, nd->path.dentry, flags & LOOKUP_PARENT);
	if (nd->root.mnt) {
		path_put(&nd->root);
		nd->root.mnt = NULL;
	}
	return retval;
}
```



注：由于`path_init`中有使用`path_get(&nd->path)`，所以要在外面配套使用`path_put`减少引用计数



## path_get 

```c
/**
 * path_get - get a reference to a path
 * @path: path to get the reference to
 *
 * Given a path increment the reference count to the dentry and the vfsmount.
 */
void path_get(struct path *path)
{
	mntget(path->mnt);
	dget(path->dentry);
}
EXPORT_SYMBOL(path_get);
```



## path_put

```c
/**
 * path_put - put a reference to a path
 * @path: path to put the reference to
 *
 * Given a path decrement the reference count to the dentry and the vfsmount.
 */
void path_put(struct path *path)
{
	dput(path->dentry);
	mntput(path->mnt);
}
EXPORT_SYMBOL(path_put);
```



## get_sb_nodev

> 创建一个新的superblock，并使用回调函数对sb进行填充

```c
int get_sb_nodev(struct file_system_type *fs_type,
	int flags, void *data,
	int (*fill_super)(struct super_block *, void *, int),
	struct vfsmount *mnt)
{
	int error;
	struct super_block *s = sget(fs_type, NULL, set_anon_super, NULL);

	if (IS_ERR(s))
		return PTR_ERR(s);

	s->s_flags = flags;

	error = fill_super(s, data, flags & MS_SILENT ? 1 : 0);
	if (error) {
		deactivate_locked_super(s);
		return error;
	}
	s->s_flags |= MS_ACTIVE;
	simple_set_mnt(mnt, s);
	return 0;
}

EXPORT_SYMBOL(get_sb_nodev);
```



## d_add/d_instantiate

> d_add 将dentry加入到父目录的hash列表里，并调用*d_instantiate*。
>
> d_instantiate 将dentry加入到对应的inode的hash列表里，并更新其d_inode字段。inode的引用计数i_count字段需要递增。该函数通常用于新创建inode给一个nagtive dentry。



## d_lookup

**定义**

```c

```

**功能**

根据pathname，查找父目录dentry下的某个dentry。如果找到，则增加引用计数并返回dentry。**调用用完该dentry之后需要通过*dput*将引用计数递减。**



**参数**

- `dentry` 



**返回值**







## d_drop

**定义**

```c
/**
 * d_drop - drop a dentry
 * @dentry: dentry to drop
 *
 * d_drop() unhashes the entry from the parent dentry hashes, so that it won't
 * be found through a VFS lookup any more. Note that this is different from
 * deleting the dentry - d_delete will try to mark the dentry negative if
 * possible, giving a successful _negative_ lookup, while d_drop will
 * just make the cache lookup fail.
 *
 * d_drop() is used mainly for stuff that wants to invalidate a dentry for some
 * reason (NFS timeouts or autofs deletes).
 *
 * __d_drop requires dentry->d_lock.
 */

static inline void __d_drop(struct dentry *dentry)
{
	if (!(dentry->d_flags & DCACHE_UNHASHED)) {
		dentry->d_flags |= DCACHE_UNHASHED;
		hlist_del_rcu(&dentry->d_hash);
	}
}

static inline void d_drop(struct dentry *dentry)
{
	spin_lock(&dcache_lock);
	spin_lock(&dentry->d_lock);
 	__d_drop(dentry);
	spin_unlock(&dentry->d_lock);
	spin_unlock(&dcache_lock);
}
```

**功能**

将dentry从其父目录的hash列表里删除。随后如果引用计数减为0，该dentry将被删除。



**参数**

- `dentry` 



**返回值**



## d_delete

**定义**

```c

```

**功能**

将dentry删除。如果引用计数不为0，则调用*d_drop*。如果为0，则调用*d_iput*将dentry搞成nagtive dentry。注意该函数不是dentry operations->d_delete函数指针，而是VFS提供的API接口。



**参数**

- `dentry` 



**返回值**







## d_get

**定义**

```c
static inline struct dentry *dget(struct dentry *dentry)
{
	if (dentry) {
		BUG_ON(!atomic_read(&dentry->d_count));
		atomic_inc(&dentry->d_count);
	}
	return dentry;
}
```



**功能**

增加dentry的引用计数



**参数**

- `dentry` 



**返回值**



## d_put

**定义**

```c
/*
 * This is dput
 *
 * This is complicated by the fact that we do not want to put
 * dentries that are no longer on any hash chain on the unused
 * list: we'd much rather just get rid of them immediately.
 *
 * However, that implies that we have to traverse the dentry
 * tree upwards to the parents which might _also_ now be
 * scheduled for deletion (it may have been only waiting for
 * its last child to go away).
 *
 * This tail recursion is done by hand as we don't want to depend
 * on the compiler to always get this right (gcc generally doesn't).
 * Real recursion would eat up our stack space.
 */

/*
 * dput - release a dentry
 * @dentry: dentry to release
 *
 * Release a dentry. This will drop the usage count and if appropriate
 * call the dentry unlink method as well as removing it from the queues and
 * releasing its resources. If the parent dentries were scheduled for release
 * they too may now get deleted.
 *
 * no dcache lock, please.
 */

void dput(struct dentry *dentry)
{
	if (!dentry)
		return;

repeat:
	if (atomic_read(&dentry->d_count) == 1)
		might_sleep();
	if (!atomic_dec_and_lock(&dentry->d_count, &dcache_lock))
		return;

	spin_lock(&dentry->d_lock);
	if (atomic_read(&dentry->d_count)) {
		spin_unlock(&dentry->d_lock);
		spin_unlock(&dcache_lock);
		return;
	}

	/*
	 * AV: ->d_delete() is _NOT_ allowed to block now.
	 */
	if (dentry->d_op && dentry->d_op->d_delete) {
		if (dentry->d_op->d_delete(dentry))
			goto unhash_it;
	}
	/* Unreachable? Get rid of it */
 	if (d_unhashed(dentry))
		goto kill_it;
  	if (list_empty(&dentry->d_lru)) {
  		dentry->d_flags |= DCACHE_REFERENCED;
		dentry_lru_add(dentry);
  	}
 	spin_unlock(&dentry->d_lock);
	spin_unlock(&dcache_lock);
	return;

unhash_it:
	__d_drop(dentry);
kill_it:
	/* if dentry was on the d_lru list delete it from there */
	dentry_lru_del(dentry);
	dentry = d_kill(dentry);
	if (dentry)
		goto repeat;
}
```



**功能**

减少`dentry`的引用计数。如果传入的`dentry`引用计数为0话，再减少就是-1了，那么此时就会进行unhash，并从链表中将此dentry删除。



**参数**

- `dentry` 



**返回值**



## fsstack_copy_attr_times/fsstack_copy_attr_atime

**定义**

```c
static inline void fsstack_copy_attr_atime(struct inode *dest,
					   const struct inode *src)
{
	dest->i_atime = src->i_atime;
}

static inline void fsstack_copy_attr_times(struct inode *dest,
					   const struct inode *src)
{
	dest->i_atime = src->i_atime;
	dest->i_mtime = src->i_mtime;
	dest->i_ctime = src->i_ctime;
}
```



**功能**

`fsstack_copy_attr_atime` 拷贝inode访问时间

`fsstack_copy_attr_times` 拷贝inode的所有时间属性



## fsstack_copy_inode_size

**定义**

```c
#include <linux/fs_stack.h>

void fsstack_copy_inode_size(struct inode *dst, const struct inode *src)
{
	i_size_write(dst, i_size_read((struct inode *)src));
	dst->i_blocks = src->i_blocks;
}
EXPORT_SYMBOL_GPL(fsstack_copy_inode_size);
```



**功能**

拷贝inode的文件大小。



## fsstack_copy_attr_all

**定义**

```c
#include <linux/fs_stack.h>

/* copy all attributes; get_nlinks is optional way to override the i_nlink
 * copying
 */
void fsstack_copy_attr_all(struct inode *dest, const struct inode *src,
				int (*get_nlinks)(struct inode *))
{
	dest->i_mode = src->i_mode;
	dest->i_uid = src->i_uid;
	dest->i_gid = src->i_gid;
	dest->i_rdev = src->i_rdev;
	dest->i_atime = src->i_atime;
	dest->i_mtime = src->i_mtime;
	dest->i_ctime = src->i_ctime;
	dest->i_blkbits = src->i_blkbits;
	dest->i_flags = src->i_flags;

	/*
	 * Update the nlinks AFTER updating the above fields, because the
	 * get_links callback may depend on them.
	 */
	if (!get_nlinks)
		dest->i_nlink = src->i_nlink;
	else
		dest->i_nlink = (*get_nlinks)(dest);
}
EXPORT_SYMBOL_GPL(fsstack_copy_attr_all);
```



**功能**

拷贝inode的除文件大小的大部分属性。






## iput

**定义**

```c
/*
 * Called when we're dropping the last reference
 * to an inode.
 *
 * Call the FS "drop()" function, defaulting to
 * the legacy UNIX filesystem behaviour..
 *
 * NOTE! NOTE! NOTE! We're called with the inode lock
 * held, and the drop function is supposed to release
 * the lock!
 */
static inline void iput_final(struct inode *inode)
{
	const struct super_operations *op = inode->i_sb->s_op;
	void (*drop)(struct inode *) = generic_drop_inode;

	if (op && op->drop_inode)
		drop = op->drop_inode;
	drop(inode);
}

/**
 *	iput	- put an inode
 *	@inode: inode to put
 *
 *	Puts an inode, dropping its usage count. If the inode use count hits
 *	zero, the inode is then freed and may also be destroyed.
 *
 *	Consequently, iput() can sleep.
 */
void iput(struct inode *inode)
{
	if (inode) {
		BUG_ON(inode->i_state == I_CLEAR);

		if (atomic_dec_and_lock(&inode->i_count, &inode_lock))
			iput_final(inode);
	}
}
EXPORT_SYMBOL(iput);
```



**功能**

减少inode的引用计数，如果inode的引用计数为0后，将调用`super_block`的操作函数将inode销毁。





## grab_cache_page_write_begin

**定义**

```c
/*
 * Find or create a page at the given pagecache position. Return the locked
 * page. This function is specifically for buffered writes.
 */
struct page *grab_cache_page_write_begin(struct address_space *mapping,
					pgoff_t index, unsigned flags)
{
	int status;
	struct page *page;
	gfp_t gfp_notmask = 0;
	if (flags & AOP_FLAG_NOFS)
		gfp_notmask = __GFP_FS;
repeat:
	page = find_lock_page(mapping, index);
	if (likely(page))
		return page;

	page = __page_cache_alloc(mapping_gfp_mask(mapping) & ~gfp_notmask);
	if (!page)
		return NULL;
	status = add_to_page_cache_lru(page, mapping, index,
						GFP_KERNEL & ~gfp_notmask);
	if (unlikely(status)) {
		page_cache_release(page);
		if (status == -EEXIST)
			goto repeat;
		return NULL;
	}
	return page;
}
EXPORT_SYMBOL(grab_cache_page_write_begin);
```



**功能**

在给定的页面缓存位置查找或创建页面。 返回锁定页面。 此函数专门用于缓冲写入。 



**参数**

- `mapping` 



**返回值**





## PageUptodate

**定义**

```c
static inline int PageUptodate(struct page *page)
{
	int ret = test_bit(PG_uptodate, &(page)->flags);

	/*
	 * Must ensure that the data we read out of the page is loaded
	 * _after_ we've loaded page->flags to check for PageUptodate.
	 * We can skip the barrier if the page is not uptodate, because
	 * we wouldn't be reading anything from it.
	 *
	 * See SetPageUptodate() for the other side of the story.
	 */
	if (ret)
		smp_rmb();

	return ret;
}
```



**功能**

判断page页是否为最新。



**参数**

- `page` 



**返回值**





## SetPageUptodate

**定义**

```c
static inline void SetPageUptodate(struct page *page)
{
#ifdef CONFIG_S390
	if (!test_and_set_bit(PG_uptodate, &page->flags))
		page_clear_dirty(page);
#else
	/*
	 * Memory barrier must be issued before setting the PG_uptodate bit,
	 * so that all previous stores issued in order to bring the page
	 * uptodate are actually visible before PageUptodate becomes true.
	 *
	 * s390 doesn't need an explicit smp_wmb here because the test and
	 * set bit already provides full barriers.
	 */
	smp_wmb();
	set_bit(PG_uptodate, &(page)->flags);
#endif
}
```



**功能**

设置page页内容为最新。



**参数**

- `page` 



**返回值**





## igrab

**定义**

```c
struct inode *igrab(struct inode *inode)
{
	spin_lock(&inode_lock);
	if (!(inode->i_state & (I_FREEING|I_CLEAR|I_WILL_FREE)))
		__iget(inode);
	else
		/*
		 * Handle the case where s_op->clear_inode is not been
		 * called yet, and somebody is calling igrab
		 * while the inode is getting freed.
		 */
		inode = NULL;
	spin_unlock(&inode_lock);
	return inode;
}
EXPORT_SYMBOL(igrab);
```



**功能**

判断inode是否有效。如果inode的状态不是`I_FREEING|I_CLEAR|I_WILL_FREE`，就返回inode，否则返回NULL。



**参数**

- `page` 



**返回值**





## iget5_locked

**定义**

```c
/**
 * iget5_locked - obtain an inode from a mounted file system
 * @sb:		super block of file system
 * @hashval:	hash value (usually inode number) to get
 * @test:	callback used for comparisons between inodes
 * @set:	callback used to initialize a new struct inode
 * @data:	opaque data pointer to pass to @test and @set
 *
 * iget5_locked() uses ifind() to search for the inode specified by @hashval
 * and @data in the inode cache and if present it is returned with an increased
 * reference count. This is a generalized version of iget_locked() for file
 * systems where the inode number is not sufficient for unique identification
 * of an inode.
 *
 * If the inode is not in cache, get_new_inode() is called to allocate a new
 * inode and this is returned locked, hashed, and with the I_NEW flag set. The
 * file system gets to fill it in before unlocking it via unlock_new_inode().
 *
 * Note both @test and @set are called with the inode_lock held, so can't sleep.
 */
struct inode *iget5_locked(struct super_block *sb, unsigned long hashval,
		int (*test)(struct inode *, void *),
		int (*set)(struct inode *, void *), void *data)
{
	struct hlist_head *head = inode_hashtable + hash(sb, hashval);
	struct inode *inode;

	inode = ifind(sb, head, test, data, 1);
	if (inode)
		return inode;
	/*
	 * get_new_inode() will do the right thing, re-trying the search
	 * in case it had to block at any point.
	 */
	return get_new_inode(sb, head, test, set, data);
}
EXPORT_SYMBOL(iget5_locked);

/*
 * Called with the inode lock held.
 * NOTE: we are not increasing the inode-refcount, you must call __iget()
 * by hand after calling find_inode now! This simplifies iunique and won't
 * add any additional branch in the common code.
 */
static struct inode *find_inode(struct super_block *sb,
				struct hlist_head *head,
				int (*test)(struct inode *, void *),
				void *data)
{
	struct hlist_node *node;
	struct inode *inode = NULL;

repeat:
	hlist_for_each_entry(inode, node, head, i_hash) {
		if (inode->i_sb != sb)
			continue;
		if (!test(inode, data))
			continue;
		if (inode->i_state & (I_FREEING|I_CLEAR|I_WILL_FREE)) {
			__wait_on_freeing_inode(inode);
			goto repeat;
		}
		break;
	}
	return node ? inode : NULL;
}

/*
 * This is called without the inode lock held.. Be careful.
 *
 * We no longer cache the sb_flags in i_flags - see fs.h
 *	-- rmk@arm.uk.linux.org
 */
static struct inode *get_new_inode(struct super_block *sb,
				struct hlist_head *head,
				int (*test)(struct inode *, void *),
				int (*set)(struct inode *, void *),
				void *data)
{
	struct inode *inode;

	inode = alloc_inode(sb);
	if (inode) {
		struct inode *old;

		spin_lock(&inode_lock);
		/* We released the lock, so.. */
		old = find_inode(sb, head, test, data);
		if (!old) {
			if (set(inode, data))
				goto set_failed;

			__inode_add_to_lists(sb, head, inode);
			inode->i_state = I_LOCK|I_NEW;
			spin_unlock(&inode_lock);

			/* Return the locked inode with I_NEW set, the
			 * caller is responsible for filling in the contents
			 */
			return inode;
		}

		/*
		 * Uhhuh, somebody else created the same inode under
		 * us. Use the old inode instead of the one we just
		 * allocated.
		 */
		__iget(old);
		spin_unlock(&inode_lock);
		destroy_inode(inode);
		inode = old;
		wait_on_inode(inode);
	}
	return inode;

set_failed:
	spin_unlock(&inode_lock);
	destroy_inode(inode);
	return NULL;
}

```



**功能**

1. 遍历全局inode链表，通过sb和data使用test回调判断是否找到inode。
2. 如果没有找到的话，则新建一个inode，并使用set回调初始化这个inode。



**参数**

- `sb` 
- `hashval`
- `test`回调函数返回1，则认为找到，否则返回0
- `set`回调函数返回0
- `data`



**返回值**



## get_fs/set_fs/get_ds/KERNLE_DS/USER_DS

```c
#include <linux/module.h>
#include <linux/init.h>
#include <linux/fs.h>
#include <linux/uaccess.h>

static char buf[] ="来自内核的访问\n";
static char buf1[32];
 
int __init test_init(void)
{
    struct file *fp;
    mm_segment_t fs;
    loff_t pos;
    printk("test enter\n");
    fp =filp_open("/home/jenkins/lubaoquan/test/kernel_file",O_RDWR | O_CREAT,0644);	// 创建用户空间文件，获取文件句柄。
    if (IS_ERR(fp)){
        printk("create file error\n");
        return -1;
    }
    fs =get_fs();		// 获取当前线程的thread_info->addr_limit。
    set_fs(KERNEL_DS);		// 将能访问的空间thread_info->addr_limit扩大到KERNEL_DS。
    pos =0;
    vfs_write(fp,buf, sizeof(buf), &pos);		// 调用vfs_write写内容
    pos =0;
    vfs_read(fp,buf1, sizeof(buf), &pos);		// 调用vfs_read读取内容
    printk("Write contet=%s\n",buf1);
    filp_close(fp,NULL);			// 关闭文件
    set_fs(fs);			// 将thread_info->addr_limit切换回原来值
    return 0;
}
void __exit test_exit(void)
{
    printk("test exit\n");
}
 
module_init(test_init);
module_exit(test_exit);
 
MODULE_LICENSE("GPL");
```



### 原理

有下面代码可知KERNEL_DS范围很大，到0xffffffffffffffff。

而USER_DS范围较小，到0x7ffffffff000。

由Linux内存分布图可知，KERNEL_DS意味着可以访问整个内存所有空间，USER_DS只能访问用户空间内存。

通过set_fs可以改变thread_info->addr_limit的大小。

```c
/*
 * For historical reasons, the following macros are grossly misnamed:
 */
#define KERNEL_DS    ((mm_segment_t) { ~0UL })        /* cf. access_ok() */
#define USER_DS        ((mm_segment_t) { TASK_SIZE-1 })    /* cf. access_ok() */

#define VERIFY_READ    0
#define VERIFY_WRITE    1

#define get_ds()  (KERNEL_DS)
#define get_fs()  (current_thread_info()->addr_limit)
#define set_fs(x) (current_thread_info()->addr_limit = (x))


#define TASK_SIZE           DEFAULT_TASK_SIZE 
```



如果把`set_fs`给注释掉，执行结果如下，可以看出fp、buf、buf1都位于内核空间。而当前空间的get_fs()为0x7ffffffff000，这些地址都超出当前空间。

所以vfs_read和vfs_write返回值都是-14，即“Bad address”。

```shell
[49001.240705] KERNEL_DS=0xffffffffffffffff USER_DS=0x7ffffffff000 get_fs()=0x7ffffffff000
[49001.240713] fp=ffff8800cae06900, buf=ffffffffc0305000 get_fs()=0x7ffffffff000
[49001.240714] ret=-14
[49001.240715] fp=ffff8800cae06900, buf1=ffffffffc03053c0
[49001.240716] ret=-14 Write contet=
[49013.464812] test exit
```



简单看一下vfs_write和vfs_read，两者都调用access_ok对地址合法性进行检查，严禁addr大于当前get_fs()。

此处buf和buf1都不满足条件，所以返回-EFAULT。

```c
#define __access_ok(addr, size, segment)                        \
({                                            \
    __chk_user_ptr(addr);                                \
    (likely((unsigned long) (addr) <= (segment).seg)                \
     && ((segment).seg == KERNEL_DS.seg                        \
         || likely(REGION_OFFSET((unsigned long) (addr)) < RGN_MAP_LIMIT)));    \
})
#define access_ok(type, addr, size)    __access_ok((addr), (size), get_fs())


ssize_t vfs_write(struct file *file, const char __user *buf, size_t count, loff_t *pos)
{
...
    if (unlikely(!access_ok(VERIFY_READ, buf, count)))
        return -EFAULT;
...
}

ssize_t vfs_read(struct file *file, char __user *buf, size_t count, loff_t *pos)
{
...
    if (unlikely(!access_ok(VERIFY_WRITE, buf, count)))
        return -EFAULT;
...
}
```



只有使用上面的方法，才能在内核中使用open,write等的系统调用。

其实这样做的主要原因是open,write的参数在用户空间，在这些系统调用的实现里需要对参数进行检查，就是检查它的参数指针地址是不是用户空间的。

系统调用本来是提供给用户空间的程序访问的，所以，对传递给它的参数（比如上面的buf、buf1），它默认会认为来自用户空间。

在vfs_write()函数中，为了保护内核空间，一般会用get_fs()得到的值来和USER_DS进行比较，从而防止用户空间程序“蓄意”破坏内核空间。

为了解决这个问题， set_fs(KERNEL_DS)将其能访问的空间限制扩大到KERNEL_DS，这样就可以在内核顺利使用系统调用了！

 

内核使用系统调用参数肯定是内核空间，为了不让这些系统调用检查参数所以必须设置 set_fs(KERNEL_DS)才能使用该系统调用。

vfs_write的流程可调用access_ok，而access_ok会判断访问的buf是否在0~addr_limit之间，如何是就ok；否则-EFAULT，这显然是为用户准备的检查。

addr_limit一般设为USER_DS，在内核空间，buf肯定>USER_DS，必须修改addr_limit，这就是set_fs的由来。



## struct kstat 在getattr和setattr中使用的结构

```c
#if defined(__KERNEL__) || !defined(__GLIBC__) || (__GLIBC__ < 2)

#define S_IFMT  00170000
#define S_IFSOCK 0140000
#define S_IFLNK	 0120000
#define S_IFREG  0100000
#define S_IFBLK  0060000
#define S_IFDIR  0040000
#define S_IFCHR  0020000
#define S_IFIFO  0010000
#define S_ISUID  0004000
#define S_ISGID  0002000
#define S_ISVTX  0001000

#define S_ISLNK(m)	(((m) & S_IFMT) == S_IFLNK)
#define S_ISREG(m)	(((m) & S_IFMT) == S_IFREG)
#define S_ISDIR(m)	(((m) & S_IFMT) == S_IFDIR)
#define S_ISCHR(m)	(((m) & S_IFMT) == S_IFCHR)
#define S_ISBLK(m)	(((m) & S_IFMT) == S_IFBLK)
#define S_ISFIFO(m)	(((m) & S_IFMT) == S_IFIFO)
#define S_ISSOCK(m)	(((m) & S_IFMT) == S_IFSOCK)

#define S_IRWXU 00700
#define S_IRUSR 00400
#define S_IWUSR 00200
#define S_IXUSR 00100

#define S_IRWXG 00070
#define S_IRGRP 00040
#define S_IWGRP 00020
#define S_IXGRP 00010

#define S_IRWXO 00007
#define S_IROTH 00004
#define S_IWOTH 00002
#define S_IXOTH 00001

#endif

#ifdef __KERNEL__
#define S_IRWXUGO	(S_IRWXU|S_IRWXG|S_IRWXO)
#define S_IALLUGO	(S_ISUID|S_ISGID|S_ISVTX|S_IRWXUGO)
#define S_IRUGO		(S_IRUSR|S_IRGRP|S_IROTH)
#define S_IWUGO		(S_IWUSR|S_IWGRP|S_IWOTH)
#define S_IXUGO		(S_IXUSR|S_IXGRP|S_IXOTH)

#define UTIME_NOW	((1l << 30) - 1l)
#define UTIME_OMIT	((1l << 30) - 2l)

#include <linux/types.h>
#include <linux/time.h>

struct kstat {
	u64		ino;
	dev_t		dev;
	umode_t		mode;
	unsigned int	nlink;
	uid_t		uid;
	gid_t		gid;
	dev_t		rdev;
	loff_t		size;
	struct timespec  atime;
	struct timespec	mtime;
	struct timespec	ctime;
	unsigned long	blksize;
	unsigned long long	blocks;
};

#endif
```



**`mode`这里是使用的八进制**

如：

```shell
-rw-r--r--. 1 root root  12150 Oct 27 07:53 sm34test.c
```

它是一个普通文件，权限是644，按照上面的定义，mode的值为`100644`，十进制为`33188`



## 打开一个文件

1. inode_permission
2. file_open
3. inode_get_attr



## 写内容到一个打开的文件

1. inode_permission
2. 写内容到文件



# 内核内存分配

[图解slub (wowotech.net)](http://www.wowotech.net/memory_management/426.html)

在Linux中，伙伴系统（buddy system）是以页为单位管理和分配内存。但是现实的需求却以字节为单位，假如我们需要申请20Bytes，总不能分配一页吧！那岂不是严重浪费内存。那么该如何分配呢？slab分配器就应运而生了，专为小内存分配而生。slab分配器分配内存以Byte为单位。但是slab分配器并没有脱离伙伴系统，而是基于伙伴系统分配的大内存进一步细分成小内存分配。

说明：slub是slab中的一种，slab也是slab中的一种。有时候用slab来统称slab, slub和slob。slab, slub和slob仅仅是分配内存策略不同。本篇文章中说的是slub分配器工作的原理。但是针对分配器管理的内存，下文统称为slab缓存池。所以文章中slub和slab会混用，表示同一个意思。



## struct kmem_cache

```c
/*
 * struct kmem_cache
 *
 * manages a cache.
 */

struct kmem_cache {
/* 1) per-cpu data, touched during every alloc/free */
	struct array_cache *array[NR_CPUS];
/* 2) Cache tunables. Protected by cache_chain_mutex */
	unsigned int batchcount;
	unsigned int limit;
	unsigned int shared;

	unsigned int buffer_size;
	u32 reciprocal_buffer_size;
/* 3) touched by every alloc & free from the backend */

	unsigned int flags;		/* constant flags */
	unsigned int num;		/* # of objs per slab */

/* 4) cache_grow/shrink */
	/* order of pgs per slab (2^n) */
	unsigned int gfporder;

	/* force GFP flags, e.g. GFP_DMA */
	gfp_t gfpflags;

	size_t colour;			/* cache colouring range */
	unsigned int colour_off;	/* colour offset */
	struct kmem_cache *slabp_cache;
	unsigned int slab_size;
	unsigned int dflags;		/* dynamic flags */

	/* constructor func */
	void (*ctor)(void *obj);

/* 5) cache creation/removal */
	const char *name;
	struct list_head next;

/* 6) statistics */
#ifdef CONFIG_DEBUG_SLAB
	unsigned long num_active;
	unsigned long num_allocations;
	unsigned long high_mark;
	unsigned long grown;
	unsigned long reaped;
	unsigned long errors;
	unsigned long max_freeable;
	unsigned long node_allocs;
	unsigned long node_frees;
	unsigned long node_overflow;
	atomic_t allochit;
	atomic_t allocmiss;
	atomic_t freehit;
	atomic_t freemiss;

	/*
	 * If debugging is enabled, then the allocator can add additional
	 * fields and/or padding to every object. buffer_size contains the total
	 * object size including these internal fields, the following two
	 * variables contain the offset to the user object and its size.
	 */
	int obj_offset;
	int obj_size;
#endif /* CONFIG_DEBUG_SLAB */

	/*
	 * We put nodelists[] at the end of kmem_cache, because we want to size
	 * this array to nr_node_ids slots instead of MAX_NUMNODES
	 * (see kmem_cache_init())
	 * We still use [MAX_NUMNODES] and not [1] or [0] because cache_cache
	 * is statically defined, so we reserve the max number of nodes.
	 */
	struct kmem_list3 *nodelists[MAX_NUMNODES];
	/*
	 * Do not add fields after nodelists[]
	 */
};
```



## slub接口

```c
struct kmem_cache *
kmem_cache_create (const char *name, size_t size, size_t align,
	unsigned long flags, void (*ctor)(void *))
void kmem_cache_destroy(struct kmem_cache *);
void *kmem_cache_alloc(struct kmem_cache *cachep, int flags);
void kmem_cache_free(struct kmem_cache *cachep, void *objp);
```



### kmem_cache_create

- name：kmem_cache的名称
- size ：slab管理对象的大小
- align：slab分配器分配内存的对齐字节数(以align字节对齐)
- flags：分配内存掩码
- ctor ：分配对象的构造回调函数



### kmem_cache_destroy

作用和kmem_cache_create相反，就是销毁创建的kmem_cache。



### kmem_cache_alloc

从cachep参数指定的kmem_cache管理的内存缓存池中分配一个对象，其中flags是分配掩码，GFP_KERNEL是不是很熟悉的掩码？



### kmem_cache_free

kmem_cache_alloc的反操作



slab分配器提供的接口该如何使用呢？其实很简单，总结分成以下几个步骤：

1) kmem_cache_create创建一个kmem_cache数据结构。

2) 使用kmem_cache_alloc接口分配内存，kmem_cache_free接口释放内存。

3) release第一步创建的kmem_cache数据结构。

再来一段demo示例代码就更好了。 

```c
/*
 *　This is a demo for how to use kmem_cache_create
 */
void slab_demo(void)
{
    struct kmem_cache *kmem_cache_16 = kmem_cache_create("kmem_cache_16", 16,
            8, ARCH_KMALLOC_FLAGS,
            NULL);
 
    /* now you can alloc memory, the buf points to 16 bytes of memory*/
    char *buf = kmeme_cache_alloc(kmem_cache_16, GFP_KERNEL);
 
    /*
     * do something what you what, don't forget to release the memory after use
*/
    kmem_cache_free(kmem_cache_16, buf);
 
    kmem_cache_destroy(kmem_cache_16);
}
```



1) 首先使用kmem_cache_create创建名称为kmem_cache_16的kmem_cache，该kmem_cache主要是描述如何管理一堆对象，其实就是slab的布局。每个对象都是16字节，并且分配的对象地址按照8字节对齐，也就是说从kmem_cache_16中分配的对象大小全是16字节。不管你要申请多少，反正就是16Bytes。当然，kmem_cache_create仅仅是创建了一个描述slab缓存池布局的数据结构，并没有从伙伴系统申请内存，具体的申请内存操作是在kmeme_cache_alloc中完成的。

2) kmeme_cache_alloc从kmem_cache_16分配一个对象。

3) 内存使用结束记得kmem_cache_free释放。

4) 如果不需要这个kmem_cache的话，就可以调用kmem_cache_destroy进行销毁吧。在释放kmem_cache之前要保证从该kmem_cache中分配的对象全部释放了，否则无法释放kmem_cache。 





# Kernel Crypto

[Kernel Crypto框架 - 内核工匠 - 博客园 (cnblogs.com)](https://www.cnblogs.com/Linux-tech/p/13326564.html)



## 常见结构

```c
struct ablkcipher_tfm {
	int (*setkey)(struct crypto_ablkcipher *tfm, const u8 *key,
	              unsigned int keylen);
	int (*encrypt)(struct ablkcipher_request *req);
	int (*decrypt)(struct ablkcipher_request *req);
	int (*givencrypt)(struct skcipher_givcrypt_request *req);
	int (*givdecrypt)(struct skcipher_givcrypt_request *req);

	struct crypto_ablkcipher *base;

	unsigned int ivsize;
	unsigned int reqsize;
};

struct aead_tfm {
	int (*setkey)(struct crypto_aead *tfm, const u8 *key,
	              unsigned int keylen);
	int (*encrypt)(struct aead_request *req);
	int (*decrypt)(struct aead_request *req);
	int (*givencrypt)(struct aead_givcrypt_request *req);
	int (*givdecrypt)(struct aead_givcrypt_request *req);

	struct crypto_aead *base;

	unsigned int ivsize;
	unsigned int authsize;
	unsigned int reqsize;
};

struct blkcipher_tfm {
	void *iv;
	int (*setkey)(struct crypto_tfm *tfm, const u8 *key,
		      unsigned int keylen);
	int (*encrypt)(struct blkcipher_desc *desc, struct scatterlist *dst,
		       struct scatterlist *src, unsigned int nbytes);
	int (*decrypt)(struct blkcipher_desc *desc, struct scatterlist *dst,
		       struct scatterlist *src, unsigned int nbytes);
};

struct cipher_tfm {
	int (*cit_setkey)(struct crypto_tfm *tfm,
	                  const u8 *key, unsigned int keylen);
	void (*cit_encrypt_one)(struct crypto_tfm *tfm, u8 *dst, const u8 *src);
	void (*cit_decrypt_one)(struct crypto_tfm *tfm, u8 *dst, const u8 *src);
};

struct hash_tfm {
	int (*init)(struct hash_desc *desc);
	int (*update)(struct hash_desc *desc,
		      struct scatterlist *sg, unsigned int nsg);
	int (*final)(struct hash_desc *desc, u8 *out);
	int (*digest)(struct hash_desc *desc, struct scatterlist *sg,
		      unsigned int nsg, u8 *out);
	int (*setkey)(struct crypto_hash *tfm, const u8 *key,
		      unsigned int keylen);
	unsigned int digestsize;
};

struct compress_tfm {
	int (*cot_compress)(struct crypto_tfm *tfm,
	                    const u8 *src, unsigned int slen,
	                    u8 *dst, unsigned int *dlen);
	int (*cot_decompress)(struct crypto_tfm *tfm,
	                      const u8 *src, unsigned int slen,
	                      u8 *dst, unsigned int *dlen);
};

struct rng_tfm {
	int (*rng_gen_random)(struct crypto_rng *tfm, u8 *rdata,
			      unsigned int dlen);
	int (*rng_reset)(struct crypto_rng *tfm, u8 *seed, unsigned int slen);
};

#define crt_ablkcipher	crt_u.ablkcipher
#define crt_aead	crt_u.aead
#define crt_blkcipher	crt_u.blkcipher
#define crt_cipher	crt_u.cipher
#define crt_hash	crt_u.hash
#define crt_compress	crt_u.compress
#define crt_rng		crt_u.rng

struct crypto_tfm {

	u32 crt_flags;

	union {
		struct ablkcipher_tfm ablkcipher;
		struct aead_tfm aead;
		struct blkcipher_tfm blkcipher;
		struct cipher_tfm cipher;
		struct hash_tfm hash;
		struct compress_tfm compress;
		struct rng_tfm rng;
	} crt_u;

	void (*exit)(struct crypto_tfm *tfm);

	struct crypto_alg *__crt_alg;

	void *__crt_ctx[] CRYPTO_MINALIGN_ATTR;
};


struct crypto_blkcipher {
	struct crypto_tfm base;
};

struct blkcipher_desc {
	struct crypto_blkcipher *tfm;
	void *info;
	u32 flags;
};


struct cipher_desc {
	struct crypto_tfm *tfm;
	void (*crfn)(struct crypto_tfm *tfm, u8 *dst, const u8 *src);
	unsigned int (*prfn)(const struct cipher_desc *desc, u8 *dst,
			     const u8 *src, unsigned int nbytes);
	void *info;
};

struct hash_desc {
	struct crypto_hash *tfm;
	u32 flags;
};
```



`xxx_desc`结构用来描述一个算法套件，真正去实现算法的时候只需要填充`crypto_alg`结构就可以，它里面只需要自己实现一个block的transform就可以了，

```c
	union {
		struct ablkcipher_tfm ablkcipher;
		struct aead_tfm aead;
		struct blkcipher_tfm blkcipher;
		struct cipher_tfm cipher;
		struct hash_tfm hash;
		struct compress_tfm compress;
		struct rng_tfm rng;
	} crt_u;
```

这里的结构体，会在调用`crypto_alloc_blkcipher`函数的时候，选则并填充指定的算法套件。

如：

```c
struct blkcipher_desc {
	struct crypto_blkcipher *tfm;
	void *info;
	u32 flags;
};
```

`info`在blockcipher中被用来存储`iv`



## 常用函数



### crypto_has_cipher 判断内核中是否有注册此算法

```c
static inline int crypto_has_cipher(const char *alg_name, u32 type, u32 mask)
{
	type &= ~CRYPTO_ALG_TYPE_MASK;
	type |= CRYPTO_ALG_TYPE_CIPHER;
	mask |= CRYPTO_ALG_TYPE_MASK;

	return crypto_has_alg(alg_name, type, mask);
}
```



### crypto_register_alg 注册加密算法到内核

```c
int crypto_register_alg(struct crypto_alg *alg);
```



### crypto_unregister_alg 卸载注册到内核的算法

```c
int crypto_unregister_alg(struct crypto_alg *alg);
```





### blkcipher

```c
struct blkcipher_tfm {
	void *iv;
	int (*setkey)(struct crypto_tfm *tfm, const u8 *key,
		      unsigned int keylen);
	int (*encrypt)(struct blkcipher_desc *desc, struct scatterlist *dst,
		       struct scatterlist *src, unsigned int nbytes);
	int (*decrypt)(struct blkcipher_desc *desc, struct scatterlist *dst,
		       struct scatterlist *src, unsigned int nbytes);
};
```



#### crypto_blkcipher_crt 获取块加密的一些属性

```c
static inline const char *crypto_blkcipher_name(struct crypto_blkcipher *tfm)
{
	return crypto_tfm_alg_name(crypto_blkcipher_tfm(tfm));
}

static inline struct blkcipher_tfm *crypto_blkcipher_crt(
	struct crypto_blkcipher *tfm)
{
	return &crypto_blkcipher_tfm(tfm)->crt_blkcipher;
}

static inline struct blkcipher_alg *crypto_blkcipher_alg(
	struct crypto_blkcipher *tfm)
{
	return &crypto_blkcipher_tfm(tfm)->__crt_alg->cra_blkcipher;
}

static inline unsigned int crypto_blkcipher_ivsize(struct crypto_blkcipher *tfm)
{
	return crypto_blkcipher_alg(tfm)->ivsize;
}

static inline unsigned int crypto_blkcipher_blocksize(
	struct crypto_blkcipher *tfm)
{
	return crypto_tfm_alg_blocksize(crypto_blkcipher_tfm(tfm));
}

static inline unsigned int crypto_blkcipher_alignmask(
	struct crypto_blkcipher *tfm)
{
	return crypto_tfm_alg_alignmask(crypto_blkcipher_tfm(tfm));
}

static inline u32 crypto_blkcipher_get_flags(struct crypto_blkcipher *tfm)
{
	return crypto_tfm_get_flags(crypto_blkcipher_tfm(tfm));
}
```



#### crypto_ablkcipher_setkey 设置key

```c
static inline int crypto_ablkcipher_setkey(struct crypto_ablkcipher *tfm,
					   const u8 *key, unsigned int keylen)
{
	struct ablkcipher_tfm *crt = crypto_ablkcipher_crt(tfm);

	return crt->setkey(crt->base, key, keylen);
}
```





#### crypto_blkcipher_encrypt 加解密运算

```c
static inline int crypto_blkcipher_encrypt(struct blkcipher_desc *desc,
					   struct scatterlist *dst,
					   struct scatterlist *src,
					   unsigned int nbytes)
{
	desc->info = crypto_blkcipher_crt(desc->tfm)->iv;
	return crypto_blkcipher_crt(desc->tfm)->encrypt(desc, dst, src, nbytes);
}

static inline int crypto_blkcipher_encrypt_iv(struct blkcipher_desc *desc,
					      struct scatterlist *dst,
					      struct scatterlist *src,
					      unsigned int nbytes)
{
	return crypto_blkcipher_crt(desc->tfm)->encrypt(desc, dst, src, nbytes);
}

static inline int crypto_blkcipher_decrypt(struct blkcipher_desc *desc,
					   struct scatterlist *dst,
					   struct scatterlist *src,
					   unsigned int nbytes)
{
	desc->info = crypto_blkcipher_crt(desc->tfm)->iv;
	return crypto_blkcipher_crt(desc->tfm)->decrypt(desc, dst, src, nbytes);
}

static inline int crypto_blkcipher_decrypt_iv(struct blkcipher_desc *desc,
					      struct scatterlist *dst,
					      struct scatterlist *src,
					      unsigned int nbytes)
{
	return crypto_blkcipher_crt(desc->tfm)->decrypt(desc, dst, src, nbytes);
}
```



**一般在使用cbc这样的算法的时候，最好用带`iv`的函数，不带iv的函数iv默认是16字节的`0x00`**

更详细的看源码，以cbc为例： http://opengrok.theworktips.com/source/xref/linux-2.6.32.69/crypto/cbc.c



## 内核cbc算法分析

```c
static int crypto_cbc_encrypt_segment(struct blkcipher_desc *desc,
				      struct blkcipher_walk *walk,
				      struct crypto_cipher *tfm)
{
	void (*fn)(struct crypto_tfm *, u8 *, const u8 *) =
		crypto_cipher_alg(tfm)->cia_encrypt;
	int bsize = crypto_cipher_blocksize(tfm);
	unsigned int nbytes = walk->nbytes;
	u8 *src = walk->src.virt.addr;
	u8 *dst = walk->dst.virt.addr;
	u8 *iv = walk->iv;

	do {
		crypto_xor(iv, src, bsize);
		fn(crypto_cipher_tfm(tfm), dst, iv);
		memcpy(iv, dst, bsize);

		src += bsize;
		dst += bsize;
	} while ((nbytes -= bsize) >= bsize);

	return nbytes;
}

static int crypto_cbc_encrypt_inplace(struct blkcipher_desc *desc,
				      struct blkcipher_walk *walk,
				      struct crypto_cipher *tfm)
{
	void (*fn)(struct crypto_tfm *, u8 *, const u8 *) =
		crypto_cipher_alg(tfm)->cia_encrypt;
	int bsize = crypto_cipher_blocksize(tfm);
	unsigned int nbytes = walk->nbytes;
	u8 *src = walk->src.virt.addr;
	u8 *iv = walk->iv;

	do {
		crypto_xor(src, iv, bsize);
		fn(crypto_cipher_tfm(tfm), src, src);
		iv = src;

		src += bsize;
	} while ((nbytes -= bsize) >= bsize);

	memcpy(walk->iv, iv, bsize);

	return nbytes;
}

static int crypto_cbc_encrypt(struct blkcipher_desc *desc,
			      struct scatterlist *dst, struct scatterlist *src,
			      unsigned int nbytes)
{
	struct blkcipher_walk walk;
	struct crypto_blkcipher *tfm = desc->tfm;
	struct crypto_cbc_ctx *ctx = crypto_blkcipher_ctx(tfm);
	struct crypto_cipher *child = ctx->child;
	int err;

	blkcipher_walk_init(&walk, dst, src, nbytes);
	err = blkcipher_walk_virt(desc, &walk);

	while ((nbytes = walk.nbytes)) {
		if (walk.src.virt.addr == walk.dst.virt.addr)
			nbytes = crypto_cbc_encrypt_inplace(desc, &walk, child);
		else
			nbytes = crypto_cbc_encrypt_segment(desc, &walk, child);
		err = blkcipher_walk_done(desc, &walk, nbytes);
	}

	return err;
}
```



1. 判断src和dst是不是指向同一块内存，如果是的话，就调用`crypto_cbc_encrypt_inplace`，否则调用`crypto_cbc_encrypt_segment`，这两个算法的内部基本上是一样的。
2. 以`crypto_cbc_encrypt_segment`为例
   1. 获取块大小`bsize`、`src`、`dst`、`iv`和块加密函数指针`fn`
   2. 使用`iv`和`src`做异或，结果放到`iv`中
   3. 将`iv`中的内容使用`fn`进行加密，结果存放到`dst`中
   4. `dst`的内容作为下一轮运算的`iv`





# 未分类函数



## request_key

**定义**

```c

```

**功能**

从内核中的密钥管理器中请求一个key



**参数**

- `dentry` 



**返回值**

