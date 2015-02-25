#### computeVisibleRange 算法分析：
第 1 步，计算出left,right,bottom,top。
第 2 步，计算出numSlots，并除于2赋值给index。
第 3 步，由index得position,判断position是否在第1步计算出的范围内，是的话，就把第2步计算得出的中间的index赋值给firstVisibleSlotIndex，lastVisibleSlotIndex，否则，根据滑动窗口算法改变 index 直到求组所需 index。
第 4 步，在while循环中，用第3步得到的firstVisibleSlotIndex求出position,进行和第2步相反的判断，即position若不在可视范围内，则将相应的 index 给 firstVisibleSlotIndex，否则减firstVisibleSlotIndex,直到找到最小的可视范围内的index作为firstVisibleSlotIndex。
第 5 步，在while循环中，用第3步得到的lastVisibleSlotIndex求出position,进行和第2步相反的判断，即position若不在可视范围内，则将相应的index给lastVisibleSlotIndex，否则增lastVisibleSlotIndex,直到找到可视范围内的最大的index作为lastVisibleSlotIndex。
第 6 步，进行 firstVisibleSlotIndex，lastVisibleSlotIndex的越界判断。outBufferedVisibleRange对应的是可见的。outBufferedVisibleRange对应的是0～文件夹的最大数。
#### computeVisibleItems 算法分析：
第 1 步,由 slot 计算出 position,set,当前set不为空且 slot 在有效范围，创建 bestItems，计算sortedIntersection。  
第 2 步,计算这个 slotindex 中的图片数目，取这个文件中的前 12 张图片加到 bestItems。 
第 3 步,取 bestItems 里的图片对应的 displayList 中的 displayItem，并赋值给 displayItems 数组，同时保存  position,及 j，j 是bestItems 数组中一项，范围是 0～12。
第 4 步,对于每一个文件夹，要在displayItems里有对应的12项，当文件夹内图片不足12时，余下的用null填充。
当绘制缩略图界面时，有些不同。
在第 1 步中，slotindex不再表示文件夹，这时表示具体某一张图片了，所以由slot得到的set里始终只有1项，且会调ArrayUtils.computeSortedIntersection(visibleItems, items,MAX_ITEMS_PER_SLOT,  bestItems, sTempHash);给bestItems赋值，这样第2步就在 bestItems加项动作不执行。