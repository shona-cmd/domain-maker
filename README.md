# domain-maker
      import React, { useState } from 'react';

const DomainGenerator = () => {
  const [keywords, setKeywords] = useState('');
  const [industry, setIndustry] = useState('');
  const [maxLength, setMaxLength] = useState(15);
  const [includeHyphens, setIncludeHyphens] = useState(false);
  const [includeNumbers, setIncludeNumbers] = useState(false);
  const [tlds, setTlds] = useState(['.com']);
  const [generatedDomains, setGeneratedDomains] = useState([]);
  const [favorites, setFavorites] = useState([]);

  const commonTlds = ['.com', '.net', '.org', '.io', '.co', '.app', '.dev', '.ai', '.me'];
  
  const generateDomains = () => {
    if (!keywords.trim()) {
      alert('Please enter at least one keyword');
      return;
    }
    
    const keywordsList = keywords.toLowerCase().split(',').map(k => k.trim()).filter(k => k);
    
    if (keywordsList.length === 0) {
      alert('Please enter valid keywords');
      return;
    }
    
    let domains = [];
    const industryTerms = industry ? industry.toLowerCase().split(',').map(t => t.trim()).filter(t => t) : [];
    
    // Generate basic combinations
    keywordsList.forEach(keyword => {
      tlds.forEach(tld => {
        domains.push(keyword + tld);
        
        // Add industry terms if provided
        industryTerms.forEach(term => {
          domains.push(keyword + term + tld);
          domains.push(term + keyword + tld);
        });
        
        if (includeHyphens && keyword.length > 3) {
          const parts = keyword.split(' ').filter(p => p);
          if (parts.length > 1) {
            domains.push(parts.join('-') + tld);
          }
        }
      });
    });
    
    // Generate combinations of keywords
    if (keywordsList.length > 1) {
      for (let i = 0; i < keywordsList.length; i++) {
        for (let j = i + 1; j < keywordsList.length; j++) {
          tlds.forEach(tld => {
            domains.push(keywordsList[i] + keywordsList[j] + tld);
            domains.push(keywordsList[j] + keywordsList[i] + tld);
          });
        }
      }
    }
    
    // Add some creative options
    keywordsList.forEach(keyword => {
      const creativePrefixes = ['get', 'my', 'try', 'the', 'best', 'top', 'pro'];
      const creativeSuffixes = ['hub', 'spot', 'hq', 'zone', 'place', 'space'];
      
      tlds.forEach(tld => {
        creativePrefixes.forEach(prefix => {
          domains.push(prefix + keyword + tld);
        });
        
        creativeSuffixes.forEach(suffix => {
          domains.push(keyword + suffix + tld);
        });
      });
    });
    
    // Add numbers if selected
    if (includeNumbers) {
      keywordsList.forEach(keyword => {
        tlds.forEach(tld => {
          for (let i = 1; i <= 9; i++) {
            domains.push(keyword + i + tld);
            domains.push(i + keyword + tld);
          }
        });
      });
    }
    
    // Filter by max length and remove duplicates
    domains = domains
      .filter(domain => domain.length <= maxLength)
      .filter((domain, index, self) => self.indexOf(domain) === index)
      .map(domain => domain.replace(/\s+/g, ''));
    
    // Sort by length (shortest first)
    domains.sort((a, b) => a.length - b.length);
    
    setGeneratedDomains(domains.slice(0, 50)); // Limit to 50 results
  };
  
  const toggleFavorite = (domain) => {
    if (favorites.includes(domain)) {
      setFavorites(favorites.filter(d => d !== domain));
    } else {
      setFavorites([...favorites, domain]);
    }
  };
  
  const handleTldChange = (tld) => {
    if (tlds.includes(tld)) {
      setTlds(tlds.filter(t => t !== tld));
    } else {
      setTlds([...tlds, tld]);
    }
  };

  return (
    <div className="p-4 max-w-4xl mx-auto">
      <h1 className="text-2xl font-bold mb-6 text-center">Domain Name Generator</h1>
      
      <div className="mb-6 bg-gray-50 p-4 rounded-lg shadow">
        <div className="grid grid-cols-1 gap-4 md:grid-cols-2">
          <div>
            <label className="block mb-2 font-medium">Keywords (comma separated)</label>
            <input
              type="text"
              value={keywords}
              onChange={(e) => setKeywords(e.target.value)}
              placeholder="design, creative, brand"
              className="w-full p-2 border rounded"
            />
          </div>
          
          <div>
            <label className="block mb-2 font-medium">Industry Terms (optional)</label>
            <input
              type="text"
              value={industry}
              onChange={(e) => setIndustry(e.target.value)}
              placeholder="studio, agency, labs"
              className="w-full p-2 border rounded"
            />
          </div>
        </div>
        
        <div className="mt-4 grid grid-cols-1 gap-4 md:grid-cols-3">
          <div>
            <label className="block mb-2 font-medium">Max Length</label>
            <input
              type="number"
              value={maxLength}
              onChange={(e) => setMaxLength(parseInt(e.target.value) || 15)}
              min="5"
              max="30"
              className="w-full p-2 border rounded"
            />
          </div>
          
          <div className="flex items-center">
            <input
              type="checkbox"
              id="hyphens"
              checked={includeHyphens}
              onChange={() => setIncludeHyphens(!includeHyphens)}
              className="mr-2"
            />
            <label htmlFor="hyphens">Include Hyphens</label>
          </div>
          
          <div className="flex items-center">
            <input
              type="checkbox"
              id="numbers"
              checked={includeNumbers}
              onChange={() => setIncludeNumbers(!includeNumbers)}
              className="mr-2"
            />
            <label htmlFor="numbers">Include Numbers</label>
          </div>
        </div>
        
        <div className="mt-4">
          <label className="block mb-2 font-medium">Domain Extensions</label>
          <div className="flex flex-wrap gap-2">
            {commonTlds.map(tld => (
              <label key={tld} className="inline-flex items-center">
                <input
                  type="checkbox"
                  checked={tlds.includes(tld)}
                  onChange={() => handleTldChange(tld)}
                  className="mr-1"
                />
                <span>{tld}</span>
              </label>
            ))}
          </div>
        </div>
        
        <button
          onClick={generateDomains}
          className="mt-4 w-full bg-blue-600 text-white p-2 rounded hover:bg-blue-700"
        >
          Generate Domain Names
        </button>
      </div>
      
      {generatedDomains.length > 0 && (
        <div className="mb-6">
          <h2 className="text-xl font-semibold mb-3">Generated Domains ({generatedDomains.length})</h2>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-2">
            {generatedDomains.map((domain, index) => (
              <div key={index} className="p-2 border rounded flex justify-between items-center">
                <span>{domain}</span>
                <button 
                  onClick={() => toggleFavorite(domain)}
                  className="text-gray-500 hover:text-yellow-500"
                >
                  {favorites.includes(domain) ? '★' : '☆'}
                </button>
              </div>
            ))}
          </div>
        </div>
      )}
      
      {favorites.length > 0 && (
        <div>
          <h2 className="text-xl font-semibold mb-3">Favorites ({favorites.length})</h2>
          <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-2">
            {favorites.map((domain, index) => (
              <div key={index} className="p-2 border rounded bg-yellow-50 flex justify-between items-center">
                <span>{domain}</span>
                <button 
                  onClick={() => toggleFavorite(domain)}
                  className="text-yellow-500"
                >
                  ★
                </button>
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
};

export default DomainGenerator;    
