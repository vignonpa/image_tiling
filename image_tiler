# -*- coding: utf-8 -*-
"""
Image Tiling Script
Author: Pierre-Antoine Vignon
Created: Aug 20, 2025
"""

import os
import cv2
import math
import numpy as np

def is_mostly_black(image, threshold=10, black_ratio=0.95):
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    black_pixels = np.sum(gray < threshold)
    total_pixels = gray.size
    return (black_pixels / total_pixels) > black_ratio

def split_image(image_path, output_dir, tile_size=2000, discard_small=False, discard_black=False):
    image = cv2.imread(image_path)
    if image is None:
        print(f"[ERROR] Cannot load image: {image_path}")
        return 0, 0  # tiles saved, tiles discarded
    
    height, width, _ = image.shape
    base_name = os.path.splitext(os.path.basename(image_path))[0]
    rows = math.ceil(height / tile_size)
    cols = math.ceil(width / tile_size)

    os.makedirs(output_dir, exist_ok=True)

    count_saved = 0
    count_discarded = 0
    for r in range(rows):
        for c in range(cols):
            y_start, x_start = r * tile_size, c * tile_size
            y_end, x_end = min(y_start + tile_size, height), min(x_start + tile_size, width)
            tile = image[y_start:y_end, x_start:x_end]

            if discard_small and (y_end - y_start < (tile_size-10) or x_end - x_start < (tile_size-10)):
                count_discarded += 1
                continue

            if discard_black and is_mostly_black(tile):
                count_discarded += 1
                continue

            output_filename = os.path.join(output_dir, f"{base_name}_{count_saved}.png")
            cv2.imwrite(output_filename, tile)
            count_saved += 1

    return count_saved, count_discarded

def process_directory(input_dir, output_dir, tile_size=2000, discard_small=False, discard_black=False):
    total_saved, total_discarded = 0, 0
    for filename in os.listdir(input_dir):
        if filename.lower().endswith(('png', 'jpg', 'jpeg', 'tiff', 'tif')):
            image_path = os.path.join(input_dir, filename)
            saved, discarded = split_image(image_path, output_dir, tile_size, discard_small, discard_black)
            total_saved += saved
            total_discarded += discarded

    print(f"\nProcessing complete: {total_saved} tiles saved, {total_discarded} tiles discarded.")

if __name__ == "__main__":
    input_dir = input("Input directory: ").strip()
    output_dir = input("Output directory: ").strip()
    discard_small = input("Discard small images? (yes/no): ").strip().lower() == "yes"
    tile_size = int(input("Tile size (pixels): ").strip())
    discard_black = input("Discard mostly black images? (yes/no): ").strip().lower() == "yes"

    process_directory(input_dir, output_dir, tile_size, discard_small, discard_black)
