# ConceptFlow
ConceptFlow is an interactive web-based concept map builder designed to help students and learners visually organize and connect ideas within any subject. This tool empowers users to create dynamic, node-based diagrams that represent their knowledge structures, fostering better understanding and retention through visual learning.
  code
  layout.jx
  import React from "react";
import { Link, useLocation } from "react-router-dom";
import { createPageUrl } from "@/utils";
import { Brain, Map, Share2, BookOpen, Plus } from "lucide-react";
import {
  Sidebar,
  SidebarContent,
  SidebarGroup,
  SidebarGroupContent,
  SidebarGroupLabel,
  SidebarMenu,
  SidebarMenuButton,
  SidebarMenuItem,
  SidebarHeader,
  SidebarFooter,
  SidebarProvider,
  SidebarTrigger,
} from "@/components/ui/sidebar";

const navigationItems = [
  {
    title: "My Maps",
    url: createPageUrl("Dashboard"),
    icon: Map,
  },
  {
    title: "Create Map",
    url: createPageUrl("CreateMap"),
    icon: Plus,
  },
  {
    title: "Shared Maps",
    url: createPageUrl("SharedMaps"),
    icon: Share2,
  },
];

export default function Layout({ children, currentPageName }) {
  const location = useLocation();

  return (
    <SidebarProvider>
      <div className="min-h-screen flex w-full bg-gradient-to-br from-indigo-50 via-white to-purple-50">
        <Sidebar className="border-r border-gray-100/80 bg-white/80 backdrop-blur-sm">
          <SidebarHeader className="border-b border-gray-100 p-6">
            <div className="flex items-center gap-3">
              <div className="w-10 h-10 bg-gradient-to-r from-indigo-500 to-purple-600 rounded-xl flex items-center justify-center shadow-lg">
                <Brain className="w-6 h-6 text-white" />
              </div>
              <div>
                <h2 className="font-bold text-gray-900 text-lg">ConceptFlow</h2>
                <p className="text-xs text-gray-500 font-medium">Visual Learning Tool</p>
              </div>
            </div>
          </SidebarHeader>
          
          <SidebarContent className="p-3">
            <SidebarGroup>
              <SidebarGroupLabel className="text-xs font-semibold text-gray-400 uppercase tracking-wider px-3 py-3">
                Navigation
              </SidebarGroupLabel>
              <SidebarGroupContent>
                <SidebarMenu className="space-y-1">
                  {navigationItems.map((item) => (
                    <SidebarMenuItem key={item.title}>
                      <SidebarMenuButton 
                        asChild 
                        className={`hover:bg-indigo-50 hover:text-indigo-700 transition-all duration-300 rounded-xl font-medium ${
                          location.pathname === item.url 
                            ? 'bg-gradient-to-r from-indigo-500 to-purple-600 text-white shadow-md' 
                            : 'text-gray-600'
                        }`}
                      >
                        <Link to={item.url} className="flex items-center gap-3 px-4 py-3">
                          <item.icon className="w-5 h-5" />
                          <span>{item.title}</span>
                        </Link>
                      </SidebarMenuButton>
                    </SidebarMenuItem>
                  ))}
                </SidebarMenu>
              </SidebarGroupContent>
            </SidebarGroup>

            <SidebarGroup className="mt-8">
              <SidebarGroupLabel className="text-xs font-semibold text-gray-400 uppercase tracking-wider px-3 py-3">
                Features
              </SidebarGroupLabel>
              <SidebarGroupContent>
                <div className="px-4 py-3 space-y-3">
                  <div className="flex items-center gap-3 text-sm text-gray-600">
                    <BookOpen className="w-4 h-4 text-indigo-500" />
                    <span>Visual Learning</span>
                  </div>
                  <div className="flex items-center gap-3 text-sm text-gray-600">
                    <Share2 className="w-4 h-4 text-purple-500" />
                    <span>Collaborative Maps</span>
                  </div>
                  <div className="flex items-center gap-3 text-sm text-gray-600">
                    <Brain className="w-4 h-4 text-pink-500" />
                    <span>Knowledge Mapping</span>
                  </div>
                </div>
              </SidebarGroupContent>
            </SidebarGroup>
          </SidebarContent>

          <SidebarFooter className="border-t border-gray-100 p-4">
            <div className="flex items-center gap-3">
              <div className="w-8 h-8 bg-gradient-to-r from-indigo-400 to-purple-500 rounded-full flex items-center justify-center">
                <span className="text-white font-medium text-sm">S</span>
              </div>
              <div className="flex-1 min-w-0">
                <p className="font-medium text-gray-900 text-sm truncate">Student</p>
                <p className="text-xs text-gray-500 truncate">Visual learner</p>
              </div>
            </div>
          </SidebarFooter>
        </Sidebar>

        <main className="flex-1 flex flex-col">
          <header className="bg-white/80 backdrop-blur-sm border-b border-gray-100 px-6 py-4 md:hidden">
            <div className="flex items-center gap-4">
              <SidebarTrigger className="hover:bg-gray-100 p-2 rounded-lg transition-colors duration-200" />
              <h1 className="text-xl font-bold text-gray-900">ConceptFlow</h1>
            </div>
          </header>

          <div className="flex-1 overflow-auto">
            {children}
          </div>
        </main>
      </div>
    </SidebarProvider>
  );
}

dashboard.jx 
import React, { useState, useEffect } from "react";
import { ConceptMap } from "@/entities/ConceptMap";
import { Button } from "@/components/ui/button";
import { Link } from "react-router-dom";
import { createPageUrl } from "@/utils";
import { Plus, Map, Share2, Eye, Calendar, Users, BookOpen } from "lucide-react";
import { format } from "date-fns";

import MapCard from "../components/dashboard/MapCard";
import StatsOverview from "../components/dashboard/StatsOverview";
import RecentActivity from "../components/dashboard/RecentActivity";

export default function Dashboard() {
  const [maps, setMaps] = useState([]);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    loadMaps();
  }, []);

  const loadMaps = async () => {
    setIsLoading(true);
    const data = await ConceptMap.list("-created_date");
    setMaps(data);
    setIsLoading(false);
  };

  const stats = {
    totalMaps: maps.length,
    publicMaps: maps.filter(m => m.is_public).length,
    sharedMaps: maps.filter(m => m.shared_with?.length > 0).length,
    totalNodes: maps.reduce((sum, map) => sum + (map.nodes?.length || 0), 0)
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-indigo-50 via-white to-purple-50 p-6">
      <div className="max-w-7xl mx-auto">
        {/* Header */}
        <div className="flex flex-col md:flex-row justify-between items-start md:items-center mb-8 gap-4">
          <div>
            <h1 className="text-4xl font-bold bg-gradient-to-r from-indigo-600 to-purple-600 bg-clip-text text-transparent">
              My Concept Maps
            </h1>
            <p className="text-gray-600 mt-2 font-medium">
              Visualize your knowledge and connect ideas
            </p>
          </div>
          <Link to={createPageUrl("CreateMap")} className="w-full md:w-auto">
            <Button className="w-full md:w-auto bg-gradient-to-r from-indigo-500 to-purple-600 hover:from-indigo-600 hover:to-purple-700 shadow-lg hover:shadow-xl transition-all duration-300">
              <Plus className="w-5 h-5 mr-2" />
              Create New Map
            </Button>
          </Link>
        </div>

        {/* Stats Overview */}
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
          <StatsOverview 
            title="Total Maps" 
            value={stats.totalMaps}
            icon={Map}
            color="from-blue-500 to-blue-600"
          />
          <StatsOverview 
            title="Total Concepts" 
            value={stats.totalNodes}
            icon={BookOpen}
            color="from-green-500 to-green-600"
          />
          <StatsOverview 
            title="Shared Maps" 
            value={stats.sharedMaps}
            icon={Share2}
            color="from-purple-500 to-purple-600"
          />
          <StatsOverview 
            title="Public Maps" 
            value={stats.publicMaps}
            icon={Eye}
            color="from-pink-500 to-pink-600"
          />
        </div>

        {/* Main Content */}
        <div className="grid lg:grid-cols-3 gap-8">
          {/* Maps Grid */}
          <div className="lg:col-span-2">
            <div className="bg-white/80 backdrop-blur-sm rounded-2xl shadow-xl border border-gray-100 p-6">
              <div className="flex justify-between items-center mb-6">
                <h2 className="text-2xl font-bold text-gray-900">Recent Maps</h2>
                <Button variant="outline" size="sm" className="hover:bg-indigo-50">
                  View All
                </Button>
              </div>
              
              {isLoading ? (
                <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                  {Array(4).fill(0).map((_, i) => (
                    <div key={i} className="animate-pulse">
                      <div className="bg-gray-200 rounded-xl h-48"></div>
                      <div className="h-4 bg-gray-200 rounded mt-4"></div>
                      <div className="h-3 bg-gray-200 rounded mt-2 w-3/4"></div>
                    </div>
                  ))}
                </div>
              ) : maps.length === 0 ? (
                <div className="text-center py-12">
                  <Map className="w-16 h-16 mx-auto text-gray-300 mb-4" />
                  <h3 className="text-xl font-semibold text-gray-600 mb-2">No concept maps yet</h3>
                  <p className="text-gray-500 mb-6">Create your first concept map to get started</p>
                  <Link to={createPageUrl("CreateMap")}>
                    <Button className="bg-gradient-to-r from-indigo-500 to-purple-600 hover:from-indigo-600 hover:to-purple-700">
                      <Plus className="w-5 h-5 mr-2" />
                      Create Your First Map
                    </Button>
                  </Link>
                </div>
              ) : (
                <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                  {maps.map((map) => (
                    <MapCard key={map.id} map={map} onUpdate={loadMaps} />
                  ))}
                </div>
              )}
            </div>
          </div>

          {/* Sidebar */}
          <div className="space-y-6">
            <RecentActivity maps={maps} />
          </div>
        </div>
      </div>
    </div>
  );
}
mapcard.jx
import React from 'react';
import { Link } from "react-router-dom";
import { createPageUrl } from "@/utils";
import { Card } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import { Share2, Eye, Users, BookOpen, ExternalLink } from "lucide-react";
import { format } from "date-fns";

export default function MapCard({ map }) {
  const nodeCount = map.nodes?.length || 0;
  const connectionCount = map.connections?.length || 0;
  
  return (
    <Card className="group relative overflow-hidden bg-white border border-gray-100 hover:shadow-xl transition-all duration-300 hover:-translate-y-1">
      {/* Visual preview area */}
      <div className="h-32 bg-gradient-to-br from-indigo-50 via-purple-50 to-pink-50 p-4 relative overflow-hidden">
        {/* Simple node visualization */}
        <div className="absolute inset-4 flex items-center justify-center">
          <div className="flex items-center gap-2">
            {Array(Math.min(nodeCount, 5)).fill(0).map((_, i) => (
              <div
                key={i}
                className={`w-3 h-3 rounded-full bg-gradient-to-r ${
                  ['from-blue-400 to-blue-500', 'from-green-400 to-green-500', 'from-purple-400 to-purple-500', 'from-pink-400 to-pink-500', 'from-yellow-400 to-yellow-500'][i % 5]
                }`}
              />
            ))}
            {nodeCount > 5 && (
              <span className="text-xs text-gray-500 font-medium">+{nodeCount - 5}</span>
            )}
          </div>
        </div>
        
        <div className="absolute top-2 right-2 flex gap-1">
          {map.is_public && (
            <Badge variant="secondary" className="bg-green-100 text-green-700 text-xs">
              <Eye className="w-3 h-3 mr-1" />
              Public
            </Badge>
          )}
          {map.shared_with?.length > 0 && (
            <Badge variant="secondary" className="bg-blue-100 text-blue-700 text-xs">
              <Users className="w-3 h-3 mr-1" />
              Shared
            </Badge>
          )}
        </div>
      </div>
      
      {/* Content */}
      <div className="p-4">
        <div className="mb-3">
          <h3 className="font-bold text-gray-900 text-lg mb-1 line-clamp-1">{map.title}</h3>
          {map.subject && (
            <p className="text-sm text-indigo-600 font-medium">{map.subject}</p>
          )}
          {map.description && (
            <p className="text-sm text-gray-600 line-clamp-2 mt-1">{map.description}</p>
          )}
        </div>
        
        {/* Stats */}
        <div className="flex gap-4 text-xs text-gray-500 mb-4">
          <div className="flex items-center gap-1">
            <BookOpen className="w-3 h-3" />
            <span>{nodeCount} concepts</span>
          </div>
          <div className="flex items-center gap-1">
            <Share2 className="w-3 h-3" />
            <span>{connectionCount} connections</span>
          </div>
        </div>
        
        {/* Date and action */}
        <div className="flex justify-between items-center">
          <p className="text-xs text-gray-400">
            {format(new Date(map.created_date), "MMM d, yyyy")}
          </p>
          <Link to={createPageUrl(`EditMap?id=${map.id}`)}>
            <Button 
              size="sm" 
              className="bg-gradient-to-r from-indigo-500 to-purple-600 hover:from-indigo-600 hover:to-purple-700 text-white group-hover:shadow-md transition-all duration-300"
            >
              Open Map
              <ExternalLink className="w-3 h-3 ml-1" />
            </Button>
          </Link>
        </div>
      </div>
    </Card>
  );
}
stats overview.jx
import React from 'react';
import { Card } from "@/components/ui/card";

export default function StatsOverview({ title, value, icon: Icon, color }) {
  return (
    <Card className="relative overflow-hidden bg-white/80 backdrop-blur-sm border border-gray-100 hover:shadow-xl transition-all duration-300">
      <div className={`absolute top-0 right-0 w-32 h-32 bg-gradient-to-br ${color} opacity-10 rounded-full transform translate-x-8 -translate-y-8`}></div>
      <div className="p-6 relative">
        <div className="flex justify-between items-start">
          <div>
            <p className="text-sm font-semibold text-gray-500 uppercase tracking-wider mb-2">{title}</p>
            <p className="text-3xl font-bold text-gray-900">{value}</p>
          </div>
          <div className={`p-3 rounded-xl bg-gradient-to-r ${color} shadow-lg`}>
            <Icon className="w-6 h-6 text-white" />
          </div>
        </div>
      </div>
    </Card>
  );
}
recent activity.jx
import React from 'react';
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Clock, Map, Share2, Eye } from "lucide-react";
import { format } from "date-fns";

export default function RecentActivity({ maps }) {
  const recentMaps = maps.slice(0, 5);

  return (
    <Card className="bg-white/80 backdrop-blur-sm border border-gray-100">
      <CardHeader>
        <CardTitle className="flex items-center gap-2 text-lg">
          <Clock className="w-5 h-5 text-indigo-500" />
          Recent Activity
        </CardTitle>
      </CardHeader>
      <CardContent>
        <div className="space-y-4">
          {recentMaps.map((map) => (
            <div key={map.id} className="flex items-center gap-3 p-3 rounded-lg hover:bg-gray-50 transition-colors">
              <div className="w-8 h-8 bg-gradient-to-r from-indigo-400 to-purple-500 rounded-lg flex items-center justify-center">
                <Map className="w-4 h-4 text-white" />
              </div>
              <div className="flex-1 min-w-0">
                <p className="font-medium text-gray-900 truncate">{map.title}</p>
                <p className="text-sm text-gray-500">
                  {format(new Date(map.created_date), "MMM d, h:mm a")}
                </p>
              </div>
              <div className="flex gap-1">
                {map.is_public && <Eye className="w-3 h-3 text-green-500" />}
                {map.shared_with?.length > 0 && <Share2 className="w-3 h-3 text-blue-500" />}
              </div>
            </div>
          ))}
          {recentMaps.length === 0 && (
            <p className="text-gray-500 text-center py-4">No recent activity</p>
          )}
        </div>
      </CardContent>
    </Card>
  );
}
