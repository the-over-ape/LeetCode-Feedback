---
name: Bug report (English)
about: Create a bug report to help us improve our content.
title: 'evaluate-division missing solution with better runtime'
---


#### user7453IM
<!-- Your LeetCode username -->


#### Category of the bug
- [ ] Question
- [x] Solution
- [ ] Language
- [ ] Missing Test Cases 


#### Description of the bug
In this question: https://leetcode.com/problems/evaluate-division/solution/
Neither of the solutions have optimal runtime O(N+M).

The code was reviewed here:
https://stackoverflow.com/questions/67091377/how-to-prove-disprove-this-algorithm-time-complexity-is-omn-amortized?noredirect=1#comment118589962_67091377
with explanations

#### Code you used for Submit/Run operation
<!-- 
Please make sure you wrap your code with ``` tags. 
Otherwise we may reject your request. 
-->

```
class Solution {
public:
        // component string -> val 
        // components maps string "captain" --> component
        typedef unordered_map<string,double> component;     // Letter --> value 
        typedef unordered_map<string,component> components; // component captain --> component
        typedef unordered_map<string,string> component_map; // var --> component captain
        typedef unordered_map<string, vector<pair<string,double>>> adjacency_list;
    
    void DFS(const string& node, component& compo, adjacency_list& adj, double value, unordered_set<string>& visited,component_map& m, const string& captain)
    {
        for(auto p: adj[node])
        {
            if(compo.find(p.first) == compo.end())
            {
                visited.insert(p.first);
                m.insert({p.first,captain}); // this letter belongs to this "captain component"
                compo.insert({p.first,value*p.second}); // insert L,V
                DFS(p.first,compo,adj,value*p.second,visited,m,captain);   
            }
        }   
    }
    
    vector<double> calcEquation(vector<vector<string>>& equations, vector<double>& values, vector<vector<string>>& queries)
    {
        adjacency_list adj;
        for(int i=0;i<equations.size();++i)
        {
            string a = equations[i][0];
            string b = equations[i][1];
            double v = values[i];
            
            auto it = adj.find(a);
            if( it == adj.end())
            {
                adj.insert({a,{}});
                it = adj.find(a);
            }
            it->second.push_back({b,v});
            
            it = adj.find(b);
            if( it == adj.end())
            {
                adj.insert({b,{}});
                it = adj.find(b);
            }
            it->second.push_back({a,1/v});
        }
        
        components cps;
        unordered_set<string> visited;
        component_map m;
        
        for(int i=0;i<equations.size();++i)
        {
            string a = equations[i][0];
            if(visited.find(a)==visited.end())
            {
                auto it = cps.insert({a,{}}).first;
                DFS(a,it->second,adj,1,visited,m,a);
            }
            
            string b = equations[i][1];
            if(visited.find(b)==visited.end())
            {
                auto it = cps.insert({b,{}}).first;
                DFS(b,it->second,adj,1,visited,m,a);
            }
        }
        
        vector<double> res;
        for(auto& q:queries)
        {
            auto it0 = m.find(q[0]);
            auto it1 = m.find(q[1]);
            
            if(it0 != m.end() && it1 != m.end() && it0->second == it1->second)
            {
                auto& captain = it0->second;
                auto& cp = cps[captain];
                res.push_back(cp[q[1]]/cp[q[0]]);
            }
            else
            {
                res.push_back(-1.0);
            }
        }
        
        return res;
        
    }
};
```

#### Language used for code
<!-- C++ -->


#### Expected behavior
<!-- A clear and concise description of what you expected to happen in
contrast with what actually happened. -->



#### Screenshots
<!-- If applicable, add screenshots to explain your issue. -->



#### Additional context
<!-- Add any other additional context about the bug. -->
