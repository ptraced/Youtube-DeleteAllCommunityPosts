# Youtube Delete All Community Posts

Paste in chrome console in https://www.youtube.com/@YourUsernameHere/community

```js
async function deleteAllCommunityPosts() {
    async function scrollUntilNoNewPosts() {
        let lastHeight = document.body.scrollHeight;
        const maxScrollAttempts = 10; // Adjust if needed
        for (let i = 0; i < maxScrollAttempts; i++) {
            window.scrollTo(0, document.body.scrollHeight);
            await new Promise(r => setTimeout(r, 2000));
            const newHeight = document.body.scrollHeight;
            if (newHeight === lastHeight) break;
            lastHeight = newHeight;
        }
    }

    async function clickElement(element) {
        element.scrollIntoView({ behavior: 'smooth', block: 'center' });
        await new Promise(r => setTimeout(r, 500));
        element.click();
        await new Promise(r => setTimeout(r, 1000));
    }

    while (true) {
        await scrollUntilNoNewPosts();
        const menuButtons = document.querySelectorAll('yt-icon-button.ytd-menu-renderer');
        if (menuButtons.length === 0) {
            console.log('No more posts found. All done!');
            break;
        }
        console.log(`Found ${menuButtons.length} posts to delete`);

        for (const menuButton of Array.from(menuButtons)) {
            try {
                await clickElement(menuButton);
                const deleteButton = Array.from(document.querySelectorAll('tp-yt-paper-item')).find(
                    item => item.textContent.includes('Delete')
                );
                if (!deleteButton) throw new Error('Delete button not found');
                await clickElement(deleteButton);

                const confirmButton = document.querySelector('#confirm-button');
                if (!confirmButton) throw new Error('Confirm button not found');
                await clickElement(confirmButton);
            } catch (error) {
                console.error('Error deleting post:', error);
            }
        }

        // Wait for a bit before continuing to ensure all deletions are processed
        await new Promise(r => setTimeout(r, 3000));
    }
}

deleteAllCommunityPosts();
```
