# InstagramUnlike

(async function unlikeWithMouseEvents() {
  const delay = ms => new Promise(res => setTimeout(res, ms));

  const randomDelay = (min, max) => {
    return delay(Math.floor(Math.random() * (max - min + 1)) + min);
  }

  let totalUnliked = 0;

  // Repeat the task 5 times with proper delays between each repetition
  for (let repeat = 0; repeat < 10; repeat++) {
    console.log(`Starting repetition ${repeat + 1}...`);

    // Click "Select" if available inside the loop
    const selectBtn = [...document.querySelectorAll('span')]
      .find(el => el.textContent.trim().toLowerCase() === 'select');
    if (selectBtn?.parentElement) {
      selectBtn.parentElement.click();
      await randomDelay(1500, 2000);
    }

    for (let batch = 0; batch < 10; batch++) {
      const circles = [...document.querySelectorAll('div[style*="circle__outline__24-4x.png"]')];
      console.log(`🎯 Found ${circles.length} selectable circles in repetition ${repeat + 1}, batch ${batch + 1}...`);
      if (circles.length === 0) break;

      for (const circle of circles) {
        circle.click();
        await randomDelay(200, 400); // Small random delay between clicks
      }

      const unlikeBtn = [...document.querySelectorAll('span')]
        .find(el => el.textContent.trim().toLowerCase() === 'unlike');
      if (unlikeBtn) {
        unlikeBtn.click();
        await randomDelay(2000, 3000); // Wait for modal to load

        // Now handle the modal
        const confirmUnlike = [...document.querySelectorAll('div')]
          .find(el =>
            el.textContent.trim().toLowerCase() === 'unlike' &&
            el.classList.contains('_ap3a') // Ensuring it's the correct confirmation button
          );

        if (confirmUnlike) {
          // Simulate the click on the 'Unlike' confirmation div
          const simulateClick = el => {
            ['mouseover', 'mousedown', 'mouseup', 'click'].forEach(type => {
              el.dispatchEvent(new MouseEvent(type, { bubbles: true, cancelable: true, view: window }));
            });
          };

          simulateClick(confirmUnlike);
          console.log(`💔 Confirmed unlike for ${circles.length} posts in batch ${batch + 1} in repetition ${repeat + 1}`);
          totalUnliked += circles.length;
        } else {
          console.log("⚠️ Confirmation unlike button not found.");
        }

        await randomDelay(3500, 5000);  // Random wait before continuing to the next batch
      } else {
        console.log("⚠️ Unlike button at bottom right not found.");
      }

      // Scroll behavior with some randomness
      window.scrollTo(0, Math.random() * document.body.scrollHeight);
      await randomDelay(4000, 5000);  // Random wait for page to refresh or load more posts
    }

    console.log(`✅ Repetition ${repeat + 1} complete. Total unliked posts: ${totalUnliked}`);
    await randomDelay(10000, 15000);  // Random delay between repetitions to avoid rate limiting
  }

  console.log(`✅ Finished. Total unliked posts after 5 repetitions: ${totalUnliked}`);
})();
