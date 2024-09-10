---
permalink: /
title: "Caitlin Raymond"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---


Student Studying Computer Engineering at the University of Miami.

## About

Hey! My name is Caitlin Raymond, I'm a sophmore at the University of Miami and I'm studying Computer Engineering. I'm originally from Naples, Florida, and some of my hobbies include:

### Music 



<!-- This is where the image will be displayed -->
<img id="dailyPicture" alt="Daily Picture" style="max-width: 100%; height: auto;">

<script>
    document.addEventListener('DOMContentLoaded', function() {
        // Array of image filenames
        const images = ['Calculator.png' , 'Desmos.png'];

        // Get today's date and calculate the day of the year
        const today = new Date();
        const start = new Date(today.getFullYear(), 0, 0);
        const diff = today - start;
        const oneDay = 1000 * 60 * 60 * 24;
        const dayOfYear = Math.floor(diff / oneDay);

        // Choose an image based on the day of the year, looping through the images array
        const imageIndex = dayOfYear % images.length;
        const imageSrc = '/images' + images[imageIndex];

        // Set the src attribute of the image element
        document.getElementById('dailyPicture').src = imageSrc;
    });
</script>



<script>
    document.addEventListener('DOMContentLoaded', function() {
        // Create a new paragraph element
        var p = document.createElement('p');
        
        // Set its text content
        p.textContent = 'JavaScript is working!';
        
        // Append the paragraph to the body
        document.body.appendChild(p);
    });
</script>




### Skating 

<iframe src="https://giphy.com/embed/3o7qDFoXt22QsZVey4" width="480" height="269" style="" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/kingoftheroad-viceland-king-of-the-road-3o7qDFoXt22QsZVey4">via GIPHY</a></p>

### Cooking

 <br/><img src='/images/Cooking.gif'>

## Porfolio

Check out some cool projects I've been working on both in and out of the classroom
