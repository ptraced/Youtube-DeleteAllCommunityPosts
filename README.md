# Youtube Delete All Community Posts

Paste in chrome console in https://www.youtube.com/@YourUsernameHere/community

```js
async function deleteAllCommunityPosts() {
    async function scrollUntilNoNewPosts() {
        let lastHeight = document.body.scrollHeight;
        let scrollAttempts = 0;
        const maxScrollAttempts = 10; // Adjust if needed

        while (scrollAttempts < maxScrollAttempts) {
            window.scrollTo(0, document.body.scrollHeight);
            await new Promise(r => setTimeout(r, 2000));
            
            let newHeight = document.body.scrollHeight;
            if (newHeight === lastHeight) {
                scrollAttempts++;
            } else {
                scrollAttempts = 0;
                lastHeight = newHeight;
            }
        }
    }

    while (true) {
        // Scroll multiple times to ensure all posts are loaded
        await scrollUntilNoNewPosts();

        // Get all menu buttons for posts
        const menuButtons = document.querySelectorAll('yt-icon-button.ytd-menu-renderer');
        
        if (menuButtons.length === 0) {
            console.log('No more posts found. All done!');
            break;
        }

        console.log(`Found ${menuButtons.length} posts to delete`);

        for (const menuButton of menuButtons) {
            try {
                // Scroll the post into view to ensure it's clickable
                menuButton.scrollIntoView({ behavior: 'smooth', block: 'center' });
                await new Promise(r => setTimeout(r, 500));
                
                // Click menu button to open options
                menuButton.click();
                await new Promise(r => setTimeout(r, 1000));
                
                // Find and click delete button
                const deleteButton = Array.from(document.querySelectorAll('tp-yt-paper-item')).find(
                    item => item.textContent.includes('Delete')
                );
                if (deleteButton) {
                    deleteButton.click();
                    await new Promise(r => setTimeout(r, 1000));
                    
                    // Confirm deletion
                    const confirmButton = document.querySelector('#confirm-button');
                    if (confirmButton) {
                        confirmButton.click();
                        await new Promise(r => setTimeout(r, 2000));
                    }
                }
            } catch (error) {
                console.error('Error deleting post:', error);
            }
        }

        // Refresh the page to ensure clean state
        await new Promise(r => setTimeout(r, 3000));
        location.reload();
        await new Promise(r => setTimeout(r, 5000));
    }
}

deleteAllCommunityPosts();
```
